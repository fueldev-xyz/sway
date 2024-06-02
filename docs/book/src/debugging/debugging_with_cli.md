# 使用 CLI 进行调试

`forc debug` CLI 可以在运行的 Fuel Client 节点上调试活动事务。

## 一个示例项目

首先，我们需要一个要调试的项目，因此使用以下命令创建一个新项目：

```bash
forc new --script dbg_example && cd dbg_example
```

然后在 `src/main.sw` 中添加一些内容，例如：

```sway
script;

use std::logging::log;

fn factorial(n: u64) -> u64 {
    let mut result = 1;
    let mut counter = 0;
    while counter < n {
        counter = counter + 1;
        result = result * counter;
    }
    return result;
}

fn main() {
    log::<u64>(factorial(5)); // 120
}
```

## 构建和字节码输出

现在我们已经准备好构建项目了。

```bash
forc build
```

接下来，生成的二进制文件应该位于 `out/debug/dbg_example.bin`。因为我们对生成的字节码感兴趣，所以可以用以下命令读取它：

```bash
forc parse-bytecode out/debug/dbg_example.bin
```

会有如下返回信息

```text

  half-word   byte   op                                    raw           notes
          0   0      JI { imm: 4 }                         90 00 00 04   jump to byte 16
          1   4      NOOP                                  47 00 00 00
          2   8      InvalidOpcode                         00 00 00 00   data section offset lo (0)
          3   12     InvalidOpcode                         00 00 00 44   data section offset hi (68)
          4   16     LW { ra: 63, rb: 12, imm: 1 }         5d fc c0 01
          5   20     ADD { ra: 63, rb: 63, rc: 12 }        10 ff f3 00
          6   24     MOVE { ra: 18, rb: 1 }                1a 48 10 00
          7   28     MOVE { ra: 17, rb: 0 }                1a 44 00 00
          8   32     LW { ra: 16, rb: 63, imm: 0 }         5d 43 f0 00
          9   36     LT { ra: 16, rb: 17, rc: 16 }         16 41 14 00
         10   40     JNZI { ra: 16, imm: 13 }              73 40 00 0d   conditionally jump to byte 52
         11   44     LOG { ra: 18, rb: 0, rc: 0, rd: 0 }   33 48 00 00
         12   48     RET { ra: 0 }                         24 00 00 00
         13   52     ADD { ra: 17, rb: 17, rc: 1 }         10 45 10 40
         14   56     MUL { ra: 18, rb: 18, rc: 17 }        1b 49 24 40
         15   60     JI { imm: 8 }                         90 00 00 08   jump to byte 32
         16   64     NOOP                                  47 00 00 00
         17   68     InvalidOpcode                         00 00 00 00
         18   72     InvalidOpcode                         00 00 00 05
```

我们可以通过条件跳转 `JNZI` 来识别 `while` 循环。第一个跳转之前的条件可以通过 `LT` 指令来识别（表示 `<`）。在我们的代码中，一些值得注意的指令只生成一次，其中包括用于乘法的 `MUL` 指令和来自 `log` 函数的 `LOG {.., 0, 0, 0}`。

## 设置调试环境

我们可以启动调试基础设施。在新的终端会话中运行 `fuel-core run --db-type in-memory --debug`；我们需要这个在运行，因为它实际上执行程序。现在我们可以启动调试器本身：`forc-debug`。如果一切设置正确，您应该会看到调试器提示符 (`>>`)。您可以使用 `help` 命令列出可用的命令。

现在我们想要在程序运行时检查程序。为此，我们首先需要将脚本发送给执行器，即 `fuel-core`。为此，我们需要一个 _交易规范_，`tx.json`。它看起来像这样：

```json
{
  "Script": {
    "script_gas_limit": 1000000,
    "script": [],
    "script_data": [],
    "policies": {
      "bits": "GasPrice",
      "values": [0, 0, 0, 0]
    },
    "inputs": [
      {
        "CoinSigned": {
          "utxo_id": {
            "tx_id": "c49d65de61cf04588a764b557d25cc6c6b4bc0d7429227e2a21e61c213b3a3e2",
            "output_index": 18
          },
          "owner": "f1e92c42b90934aa6372e30bc568a326f6e66a1a0288595e6e3fbd392a4f3e6e",
          "amount": 10599410012256088338,
          "asset_id": "2cafad611543e0265d89f1c2b60d9ebf5d56ad7e23d9827d6b522fd4d6e44bc3",
          "tx_pointer": {
            "block_height": 0,
            "tx_index": 0
          },
          "witness_index": 0,
          "maturity": 0,
          "predicate_gas_used": null,
          "predicate": null,
          "predicate_data": null
        }
      }
    ],
    "outputs": [],
    "witnesses": [
      {
        "data": [
          156, 254, 34, 102, 65, 96, 133, 170, 254, 105, 147, 35, 196, 199, 179,
          133, 132, 240, 208, 149, 11, 46, 30, 96, 44, 91, 121, 195, 145, 184,
          159, 235, 117, 82, 135, 41, 84, 154, 102, 61, 61, 16, 99, 123, 58,
          173, 75, 226, 219, 139, 62, 33, 41, 176, 16, 18, 132, 178, 8, 125,
          130, 169, 32, 108
        ]
      }
    ],
    "receipts_root": "0000000000000000000000000000000000000000000000000000000000000000"
  }
}
```

然而，键 `script` 应该包含要执行的实际字节码，即 `out/debug/dbg_example.bin` 的内容作为 JSON 数组。可以使用以下命令来生成它：

```bash
python3 -c 'print(list(open("out/debug/dbg_example.bin", "rb").read()))'
```

现在我们用结果替换脚本数组，并将其保存为 `tx.json`。

## 使用调试器

现在我们可以执行脚本了：

```text
>> start_tx tx.json

Receipt: Log { id: 0000000000000000000000000000000000000000000000000000000000000000, ra: 120, rb: 0, rc: 0, rd: 0, pc: 10380, is: 10336 }
Receipt: Return { id: 0000000000000000000000000000000000000000000000000000000000000000, val: 0, pc: 10384, is: 10336 }
Receipt: ScriptResult { result: Success, gas_used: 60 }
Terminated
```

查看第一行输出，我们可以看到它记录了 `ra: 120`，这是 `factorial(5)` 的正确返回值。它还告诉我们执行没有触发任何断点而终止。这并不奇怪，因为我们还没有设置任何断点。我们可以使用 `breakpoint` 命令来设置断点：

```text
>> breakpoint 0

>> start_tx tx.json

Receipt: ScriptResult { result: Success, gas_used: 0 }
Stopped on breakpoint at address 0 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

```

现在我们已经在入口处的断点处停止了执行（地址为 `0`）。现在我们可以检查虚拟机的初始状态。

```text
>> register ggas

reg[0x9] = 1000000  # ggas

>> memory 0x10 0x8

 000010: e9 5c 58 86 c8 87 26 dd
```

然而，这也不是太有趣，所以让我们执行到结束，然后重置虚拟机以删除断点。

```text
>> continue

Receipt: Log { id: 0000000000000000000000000000000000000000000000000000000000000000, ra: 120, rb: 0, rc: 0, rd: 0, pc: 10380, is: 10336 }
Receipt: Return { id: 0000000000000000000000000000000000000000000000000000000000000000, val: 0, pc: 10384, is: 10336 }
Terminated

>> reset

```

接下来，我们将设置一个断点，以便在每次`while`循环迭代时检查状态。例如，如果我们想看看哪些数字被相乘，我们可以在操作之前设置断点。字节码只有一个`MUL`指令：

```text
  half-word   byte   op                                    raw           notes
         14   56     MUL { ra: 18, rb: 18, rc: 17 }        1b 49 24 40
```

我们可以在其地址上设置一个断点，在半字偏移量`14`处。

```text
>>> breakpoint 14

>> start_tx tx.json

Receipt: ScriptResult { result: Success, gas_used: 9 }
Stopped on breakpoint at address 56 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

```

现在我们可以检查要相乘的输入。查看[规范](https://github.com/FuelLabs/fuel-specs/blob/master/src/fuel-vm/instruction-set.md#mul-multiply)告诉我们指令`MUL { ra: 18, rb: 18, rc: 17 }`表示`reg[18] = reg[18] * reg[17]`。因此，检查输入告诉我们：

```text
>> r 18 17

reg[0x12] = 1        # reg18
reg[0x11] = 1        # reg17
```

因此，在第一轮中，数字分别为`1`和`1`，因此我们可以继续到下一轮迭代：

```text
>> c

Stopped on breakpoint at address 56 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

>> r 18 17

reg[0x12] = 1        # reg18
reg[0x11] = 2        # reg17

```

接下来是：

```text
>> c

Stopped on breakpoint at address 56 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

>> r 18 17

reg[0x12] = 2        # reg18
reg[0x11] = 3        # reg17
```

第四个是：

```text
>> c

Stopped on breakpoint at address 56 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

>> r 18 17

reg[0x12] = 6        # reg18
reg[0x11] = 4        # reg17

```

第五轮是:

```text
>> c

Stopped on breakpoint at address 56 of contract 0x0000000000000000000000000000000000000000000000000000000000000000

>> r 18 17

reg[0x12] = 24       # reg18
reg[0x11] = 5        # reg17

```

此时，我们可以查看这些值：

| 17  | 18  |
| --- | --- |
| 1   | 1   |
| 2   | 1   |
| 3   | 2   |
| 4   | 6   |
| 5   | 24  |

从这里，我们清楚地看到，寄存器 `17` 是 `counter` 变量，而寄存器 `18` 是 `result`。现在，计数器等于给定的阶乘函数参数 `5`，循环终止。因此，当我们继续执行时，程序完成而不再遇到任何断点：

```text
>> c

Receipt: Log { id: 0000000000000000000000000000000000000000000000000000000000000000, ra: 120, rb: 0, rc: 0, rd: 0, pc: 10380, is: 10336 }
Receipt: Return { id: 0000000000000000000000000000000000000000000000000000000000000000, val: 0, pc: 10384, is: 10336 }
Terminated
```
