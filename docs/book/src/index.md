# Sway 编程语言

欢迎阅读 Sway 编程语言书籍 🌴。

**问：Hi！什么是 Sway？**

Sway 是一种领域特定的编程语言，用于在区块链平台上实现智能合约，尤其是为[Fuel 虚拟机（Fuel VM）](https://docs.fuel.network/docs/specs/fuel-vm/)设计的。

受到[Rust](https://doc.rust-lang.org/book/)的系统编程方法的启发，Sway 旨在将现代编程语言特性和工具带入智能合约开发，同时保持性能、精细控制，并广泛使用静态分析来防止常见的安全问题。

**问：“领域特定”是什么意思？**

Sway 专门用于区块链环境，它的行为与传统计算机非常不同。这种领域特定的设计使其能够在每个层次上做出正确的权衡决策，使您能够编写快速、安全且成本效益高的智能合约，并提供适合您特定需求的功能。

**问：为什么不用 Solidity？**

Solidity 是一个值得敬仰的先驱，但它由于与 EVM 的许多历史怪癖绑定，存在一些问题。它缺乏程序员普遍期望的常见功能，类型系统相对不够表达性，而且缺乏统一的工具生态系统。

在 Sway 中，我们让您使用全套现代工具箱设计智能合约。您将获得一个具有泛型、代数类型和基于特性的多态性的完整功能语言。您还将获得一个集成的、统一的、易于使用的工具链，包括代码补全 LSP 服务器、格式化器、文档生成器以及运行和部署合约所需的一切，以确保没有任何障碍妨碍您实现所需功能。

我们的表达性类型系统允许您捕捉语义错误，我们提供良好的默认设置，并进行广泛的静态分析检查（例如强制执行[检查、效果、交互](./blockchain-development/calling_contracts.md#cei-pattern-violation-static-analysis)模式），以确保您在编译时编写安全且正确的代码。

**问：为什么不用 Rust？**

虽然 Rust 是一种出色的系统编程语言（Sway 本身也是用 Rust 编写的），但它并不适合智能合约开发。

Rust 的优势在于它可以使用零成本抽象和复杂的借用检查器内存模型，在没有垃圾收集器的情况下为复杂程序实现令人印象深刻的运行时性能。

在区块链上，执行和部署的成本是稀缺资源。内存使用量低且执行时间短。这使得复杂的内存管理通常过于昂贵，Rust 的借用检查器在这种情况下反而成为负担，没有好处。

一般用途的编程语言普遍不适合这种环境，因为它们的设计必须假设在通用计算环境中执行。

Sway 试图为智能合约开发人员带来 Rust 的所有其他优势，包括其现代类型系统、安全性方法和良好的默认设置，通过提供适应区块链环境特定需求的熟悉语法和功能。

**问：我不懂 Rust 或 Solidity。还能学 Sway 吗？**

可以！如果您熟悉编程基础、区块链和使用终端，您就可以使用 Sway 进行开发。

**问：我可以用 Sway 构建什么？**

您可以构建智能合约及其组件和库。您可以在[程序类型](./sway-program-types/index.md)部分了解更多关于不同程序类型及其如何组合的信息。

**问：我需要安装什么吗？**

如果您想在本地环境中使用 Sway 进行开发，您需要安装[`fuelup`](https://docs.fuel.network/guides/installation/)和支持 LSP 的编辑器，如[VSCode](https://code.visualstudio.com/)。

如果您现在不想安装任何东西，可以使用[Sway Playground](https://www.sway-playground.org/)来编辑、编译和部署 Sway 代码。

**问：在哪里可以找到 Sway 代码示例？**

您可以在 GitHub 上的[Sway 应用程序仓库](https://github.com/FuelLabs/sway-applications)中找到使用 Sway 构建的示例应用程序。您还可以在[Fuel 生态系统主页](https://app.fuel.network/ecosystem)中找到构建在 Fuel 上的项目。

**问：什么是标准库？**

[标准库](./introduction/standard_library.md)，也称为`std`，是一个提供在 Sway 中开发的核心函数和帮助程序的库。标准库有自己的[参考文档](https://fuellabs.github.io/sway/master/std/)，其中详细介绍了`std`中每个模块的信息。

**问：什么是 Sway 标准？**

类似于以太坊和 Solidity 的 ERC 标准，Sway 有自己的 SRC 标准，帮助实现不同智能合约之间的互操作性。有关使用 Sway 标准的更多信息，您可以查看[Sway 标准仓库](https://github.com/FuelLabs/sway-standards)。

**问：如何创建一个代币？**

Sway 有多个本机资产。要铸造新的本机资产，请查看[本机资产](./blockchain-development/native_assets.md)页面。

**问：如何创建一个 NFT？**

您可以在[Sway 应用程序仓库](https://github.com/FuelLabs/sway-applications/tree/master/NFT)中找到 Sway 的 NFT 合约示例。

**问：如何测试 Sway 代码？**

Sway 提供[单元测试](./testing/unit-testing.md)，因此您可以使用 Sway 测试您的 Sway 代码。您还可以使用 Fuel 的[Rust SDK](https://docs.fuel.network/docs/fuels-rs/testing/)或[TypeScript SDK](https://docs.fuel.network/docs/fuels-ts/testing/)测试您的 Sway 程序。

**问：如何部署合约？**

您可以使用`forc deploy`命令部署合约。有关如何部署合约的详细指南，请参阅[快速入门指南](https://docs.fuel.network/docs/intro/quickstart-contract/)。

**问：是否有将 Solidity 代码转换为 Sway 的方法？**

有！您可以使用[Sway Playground](https://www.sway-playground.org/)中的 Solidity 到 Sway 转译器将 Solidity 代码转换为 Sway 代码。请注意，该转译器仍在试验中，可能并不适用于所有情况。

**问：如何获得 Sway 的帮助？**

如果您遇到问题或有疑问，请在[Fuel 论坛](https://forum.fuel.network/)上发布，这样 Fuel 社区中的某个人可以帮助您。

**问：应该从哪里开始？**

_准备构建？_ 您可以在[Fuel 开发者指南](https://docs.fuel.network/guides/)中找到如何使用 Sway 构建应用程序的分步指南。

_想阅读？_ 请从本书的[介绍](./introduction/index.md)和[基础](./basics/index.md)部分开始阅读。
