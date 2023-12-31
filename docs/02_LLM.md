# 02 大型语言模型

## LLMs 如何工作?

让我们看看大型语言模型(Large Language Models)是如何工作的，重点是 OpenAI GPT（生成式预训练 Transformer）模型。

- **分词器Tokenizer，文本到数字**：大型语言模型接收文本作为输入并生成文本作为输出。 然而，作为统计模型，它们对数字的处理效果对比起文本序列的处理效果要好得多。 这就是为什么模型的每个输入在被核心模型使用之前都由分词器处理。 标记token是一段chunk文本——由可变数量的字符组成，因此分词器的主要任务是将输入分割成tokens。 然后，每个标记token 都映射有一个索引，该索引是原始文本块chunk的整数编码。

![Example of tokenization](./02_LLM.assets/tokenizer-example.pngWT-1703175122317-2.png)

- **预测输出标记**：给定 n 个标记作为输入（最大 n 因模型而异），模型能够预测一个标记作为输出。 然后，该标记会以扩展窗口模式合并到下一次迭代的输入中，从而获得一个（或多个）句子作为答案,生成结果有更好的用户体验。 这解释了为什么如果您曾经使用过 ChatGPT，您可能会注意到有时它在生成结果时在句子中间出现停顿。

- **选择过程，概率分布**：模型根据其在当前文本序列之后出现的概率来选择输出标记。 这是因为该模型预测了根据其训练计算出的所有可能的“下一个标记”的概率分布。 然而，并不总是从结果分布中选择概率最高的标记。 这种选择增加了一定程度的随机性，模型以非确定性方式运行——对于相同的输入，我们不会得到完全相同的输出。 添加这种程度的随机性是为了模拟人类创造性思维的过程，您可以使用称为温度temperature的模型参数进行调整。

  - >"温度"参数用于控制生成文本的随机性或创造性。这个参数通常设置在0到1之间。让我为你解释它的工作原理：
    >
    >1. **温度为0**：在这种设置下，模型将始终选择概率最高的下一个词。这意味着生成的文本会非常一致和可预测，但可能缺乏创造性和多样性。
    >
    >2. **温度为1**：这是标准设置，模型在选择下一个词时会考虑多种可能性，即使某些词的概率稍低。这提高了文本的多样性和创造性，但同时可能增加了生成某些不那么准确或不相关内容的风险。
    >
    >3. **温度大于1**：在这种情况下，模型在生成文本时会更加随机和创造性，甚至可能选择那些在常规情况下不太可能出现的词。这可以用于生成更加原创和非常规的内容，但同时也可能导致文本质量下降，包括语法错误和不相关内容的增加。
    >
    >总的来说，温度参数是一种平衡创造性和准确性的工具。低温度倾向于产生更准确、更可预测的输出，而高温度则增加了随机性和创造性。

## 不同的LLMs

大型语言模型 (LLM) 可以根据其架构、训练数据和用例进行多种分类。 了解这些差异将有助于你根据场景选择正确的模型，并了解如何测试、迭代和提高性能。

LLM 模型有许多不同类型，您选择的模型取决于您的用途、您的数据、您准备支付的费用等等。

根据您是否打算使用模型进行文本、音频、视频、图像生成等，您可能会选择不同类型的模型。

- **音频和语音识别**。 为此，Whisper 模型是一个不错的选择，因为它们是通常用于语音识别。 它经过不同音频数据的训练，可以执行多语言语音识别。 例如，您可以使用所有的模型，从价格便宜但功能强大的模型（如 Curry）到更昂贵拥有高性能的达芬奇（Davinci）模型。 详细了解 [Whisper 类型模型](https://platform.openai.com/docs/models/whisper?WT.mc_id=academic-105485-koreyst)。
- **图像生成**。 对于图像生成，DALL-E 和 Midjourney 是两个最佳的选择。 DALL-E 由 OpenAI 提供。 [在此处阅读有关 DALL-E 的更多信息](https://platform.openai.com/docs/models/dall-e?WT.mc_id=academic-105485-koreyst) 。
- **文本生成**。 大多数模型都经过文本生成训练，您有从 GPT-3.5 到 GPT-4 的多种选择。 它们的成本各不同，其中 GPT-4 是最昂贵的。

选择模型意味着您能获得一些基本功能，但这可能还不够。 通常，您有公司特定的数据，您需要以某种方式告诉 LLMs。 关于如何解决这个问题，有几种不同的选择，接下来的部分将详细介绍。

### 基础模型

“基础模型”是[由斯坦福大学研究人员创造](https://arxiv.org/abs/2108.07258?WT.mc_id=academic-105485-koreyst)，定义为遵循某些标准的人工智能模型，例如：

- **他们使用无监督学习或自监督学习进行训练**，这意味着他们接受未标记的多模式数据的训练，并且他们的训练过程不需要人工注释或数据标记。
- **它们是非常大的模型**，基于经过数十亿参数训练的深度神经网络。
- **它们通常旨在作为其他模型的“基础”**，这意味着它们可以用作构建其他模型的起点，可以通过微调方式来完成。

![基础模型与 LLMs](./02_LLM.assets/FoundationModel.pngWT.png)

图片来源: [Essential Guide to Foundation Models and Large Language Models | by Babar M Bhatti | Medium](https://thebabar.medium.com/essential-guide-to-foundation-models-and-large-language-models-27dab58f7404)

为了进一步阐明这种区别，我们以 ChatGPT 为例。 为了构建 ChatGPT 的第一个版本，名为 GPT-3.5 的模型作为基础模型。 这意味着 OpenAI 使用一些特定于聊天的数据来创建 GPT-3.5 的调整版本，专门用于在对话场景（例如聊天机器人）中让其有更好的表现。

![image-20231222003545113](./02_LLM.assets/image-20231222003545113.png)

图片来源: [2108.07258.pdf (arxiv.org)](https://arxiv.org/pdf/2108.07258.pdf?WT.mc_id=academic-105485-koreyst)

### 开源模型与专有模型

对 LLM 进行分类的另一种方法是它们是开源的还是专有proprietary的。

开源模型是向公众开放并且任何人都可以使用的模型。 它们通常由创建它们的公司或研究团体提供。 这些模型可以针对 LLMs 的各种用例进行检查、修改和定制。 然而，它们并不总是针对生产用途进行优化，并且可能不如专有模型具备高性能。此外，开源模型的资金可能有限，并且它们可能无法长期维护或可能无法根据最新研究进行更新。 流行的开源模型的例子包括 [Alpaca](https://crfm.stanford.edu/2023/03/13/alpaca.html)、[Bloom](https://sapling.ai/llm/bloom) 和 [LLaMA](https://sapling.ai/llm/llama?WT.mc_id=academic-105485-koreyst)。

专有模型是公司拥有的模型，不向公众提供。 这些模型通常针对生产用途进行了优化。 但是，不允许针对特定的使用场景进行检查、修改或定制它们。 另外，它们并不总是免费提供，可能需要订阅或付费才能使用。 此外，用户无法控制用于训练模型的数据，这意味着他们应该委托模型所有者确保对数据隐私和负责任地使用人工智能的承诺。 流行的专有模型的例子包括 [OpenAI 模型](https://platform.openai.com/docs/models/overview)、[Google Bard](https://sapling.ai/llm/bard?WT.mc_id=academic-105485-koreyst) 或 [Claude 2]( https://www.anthropic.com/index/claude-2)。

### 嵌入、图像生成、文本和代码生成

LLMs 还可以根据其产生的输出进行分类。

**嵌入Embeddings**是一组可以将文本转换为数字形式的模型，称为嵌入，它是输入文本的数字表示。 嵌入使机器更容易理解单词或句子之间的关系，并且可以用作其他模型的输入，例如分类模型或对数值数据具有更好性能的聚类模型。 嵌入模型通常用于迁移学习，其中为有大量数据的代理任务构建模型，然后将模型权重（嵌入）重新用于其他下游任务。 此类别的一个示例是 [OpenAI 嵌入](https://platform.openai.com/docs/models/embeddings?WT.mc_id=academic-105485-koreyst)。

![嵌入](./02_LLM.assets/Embedding.pngWT.png)

**图像**生成模型主要是用来生成图像。 这些模型通常用于图像编辑、图像合成和图像翻译。 图像生成模型通常在大型图像数据集上进行训练，例如 [LAION-5B](https://laion.ai/blog/laion-5b/?WT.mc_id=academic-105485-koreyst)，并且可用于生成新图像或编辑现有图像 修复、超分辨率和着色技术。 如 [DALL-E-3](https://openai.com/dall-e-3) 和 [StableDiffusion](https://github.com/Stability-AI/StableDiffusion?WT.mc_id=academic-105485-koreyst)。

![Image generation](./02_LLM.assets/Image.pngWT.png)

**文本或代码**生成模型主要是生成文本或代码。 这些模型通常用于文本摘要、翻译和问答。 文本生成模型通常在大型文本数据集上进行训练，例如 [BookCorpus](https://www.cv-foundation.org/openaccess/content_iccv_2015/html/Zhu_Aligning_Books_and_ICCV_2015_paper.html)，并且可用于生成新文本或回答问题。 代码生成模型，例如 [CodeParrot](https://huggingface.co/codeparrot?WT.mc_id=academic-105485-koreyst)，通常在大型代码数据集（如 GitHub）上进行训练，可用于生成新代码或修复现有代码中的错误。

![Text and code generation](./02_LLM.assets/Text.pngWT.png)

### 编码-解码器与独立解码器

为了讨论 LLMs 的不同类型的架构，让我们来打个比方。

想象一下，您的经理给您一个为学生编写测验题目的任务。 您有两个同事； 一名负责监督内容的创建，另一名负责审查内容。

内容创建者就像一个独立**解码器**的模型，他们可以查看主题并查看您已经写的内容，然后他可以基于这些内容来编写课程。 他们非常擅长撰写引人入胜且内容丰富的内容，但不太擅长理解主题和学习目标。 独立解码器模型如 GPT 系列模型，例如 GPT-3。

审阅者就像一个独立**编码器**的模型，他们查看编写的课程和答案，注意它们之间的关系并通过上下文进行理解，但他们不擅于生成内容。独立编码器模型的一个例子是 BERT。

想象一下，我们也可以有人可以创建和审查测验，这是一个编码器-解码器Encoder-Decoder模型如 BART 和 T5

### 服务与模型

现在，我们来谈谈服务和模型之间的区别。 服务是云服务提供商提供的产品，通常是模型、数据和其他组件的结合。 模型是服务的核心组件，通常是基础模型，例如各种 LLM。

服务通常针对生产环境进行了优化，并且通常比模型让用户通过图形界面更容易使用。 但服务并不总是免费提供的，可能需要订阅或付费才能使用，以换取服务所有者的设备和相关资源，优化费用并轻松扩展。 服务的一个例子是 [Azure OpenAI 服务](https://learn.microsoft.com/azure/ai-services/openai/overview?WT.mc_id=academic-105485-koreyst)，它提供按需付费计划，意味着用户根据服务用量付费。此外，Azure OpenAI Service 在模型功能上还提供企业级安全性和负责任的 AI 框架。

模型是带有参数、权重等的神经网络。 允许公司在本地运营需要购买设备、构建并购买许可证或使用开源模型。 像 LLaMA 这样的模型使用就需要额外的算力运行。

## 提升 LLM 的输出结果准确度

什么时候应该考虑微调模型而不是使用预先训练的模型呢？ 是否有其他方法可以提高模型在特定工作负载workload上的性能？

企业可以使用多种方法从 LLMs 获得所需的结果，您可以选择具有不同训练程度的不同类型的模型。

在生产中部署 LLMs ，具有不同程度的复杂性、成本和质量。 以下是一些不同的方法：

- **根据上下文的提示工程**。 这个想法是在提示时提供足够的背景信息，以确保获得所需的结果。
- **检索增强生成，RAG**。 例如，您的数据可能存在于向量数据库或 Web 端点中，为了确保在提示时包含此数据或其子集，您可以获取相关数据并对用户进行提示。
- **微调模型**。 在这里，您根据自己的数据进一步训练模型，这使得模型更加准确并且能够响应您的需求，但可能成本高昂。

![LLMs deployment](./02_LLM.assets/Deploy.pngWT.png)

图片来源: [企业部署LLM的四种方式| Fiddler AI 博客](https://www.fiddler.ai/blog/four-ways-that-enterprises-deploy-llms?WT.mc_id=academic-105485-koreyst)

### 通过上下文的提示工程

预先训练的 LLMs 在广义自然语言任务上表现得非常好，甚至可以用简短的提示来调用它们，比如要完成的句子或问题——即所谓的“零样本”学习。

然而，用户越能通过详细的请求和示例（上下文）来构建他们的查询，就会得到最准确、最接近用户期望的答案。 在这种情况下，如果提示仅包含一个示例，我们讨论“单样本”学习；如果提示包含多个示例，我们讨论“少样本学习”。

根据上下文进行快速工程设计是最具成本效益的启动方法。

### 检索增强生成 (Retrieval Augmented Generation，RAG)

LLMs 有一个限制，即他们只能使用训练期间使用过的数据来生成答案。 这意味着他们对训练过程后发生的事情一无所知，并且无法访问非公开信息（例如公司数据）。 这可以通过 RAG 来克服，RAG 是一种考虑提示长度限制的技术，以文档块的形式使用外部数据来增强提示。 矢量数据库工具（例如 [Azure 向量搜索](https://learn.microsoft.com/azure/search/vector-search-overview?WT.mc_id=academic-105485-koreyst)）支持此功能，可检索有用的信息来自各种预定义数据源的块并将它们添加到提示上下文中。

当企业没有足够的数据、足够的时间或资源来微调 LLMs，但仍希望提高特定工作负载的性能并减少幻觉的风险（即对现实的神秘化或有害的内容）时，此技术非常有用。

### 微调模型

微调是一个利用迁移学习使模型“适应”下游任务或解决特定问题的过程。 与少样本学习和 RAG 不同，它会生成一个新模型，并更新权重和偏差。 它需要一组训练示例，其中包含单个输入（提示）及其关联的输出（完成）。

如果出现以下情况，这将是首选方法：

- **使用微调模型**。 企业希望使用经过微调能力较差的模型（例如嵌入模型）而不是高性能模型，从而获得更具成本效益和快速的解决方案。
- **考虑延迟**。 延迟对于特定用例很重要，因此不可能使用很长的提示，或者应该从模型中学习的示例数量不符合提示长度限制。
- **保持最新状态**。 企业拥有大量高质量的数据和真实标签，以及随着时间的推移保持这些数据最新所需的资源。

### 训练垂直行业模型

从头开始培训 LLMs 无疑是最困难、最复杂的方法，需要大量数据、熟练资源和适当的计算能力。 仅在企业具有特定领域的用例和大量以特定领域为中心的数据的情况下才应考虑此选项。

> 如果您有时间和资源以及高质量的数据，微调是保持最新状态的更好选择。 然而，如果您正在寻求改进，但又缺乏时间，那么值得首先考虑 RAG。

## 拓展学习资源

🔗 [How to use Open Source foundation models curated by Azure Machine Learning (preview) - Azure Machine Learning | Microsoft Learn](https://learn.microsoft.com/azure/machine-learning/how-to-use-foundation-models?WT.mc_id=academic-105485-koreyst)

🔗 [The Large Language Model (LLM) Index | Sapling](https://sapling.ai/llm/index?WT.mc_id=academic-105485-koreyst)

🔗 [[2304.04052\] Decoder-Only or Encoder-Decoder? Interpreting Language Model as a Regularized Encoder-Decoder (arxiv.org)](https://arxiv.org/abs/2304.04052?WT.mc_id=academic-105485-koreyst)

🔗 [Retrieval Augmented Generation using Azure Machine Learning prompt flow](https://learn.microsoft.com/azure/machine-learning/concept-retrieval-augmented-generation?WT.mc_id=academic-105485-koreyst)

🔗 [Grounding LLMs](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/grounding-llms/ba-p/3843857?wt.mc_id=github_S-1231_webpage_reactor)

🔗 [The Large Language Model (LLM) Index | Sapling](https://sapling.ai/llm/index?wt.mc_id=github_S-1231_webpage_reactor)

🔗 [[2304.04052\] Decoder-Only or Encoder-Decoder? Interpreting Language Model as a Regularized Encoder-Decoder (arxiv.org)](https://arxiv.org/abs/2304.04052?wt.mc_id=github_S-1231_webpage_reactor)
