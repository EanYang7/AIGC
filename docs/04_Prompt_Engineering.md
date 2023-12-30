# 04 提示工程

_生成式人工智能_ 能够根据用户请求创建新内容（例如文本、图像、音频、代码等）。 它使用 LLMs 来实现这一目标，例如 OpenAI 的 GPT 模型系列，这些模型通过使用自然语言和代码进行训练。

用户现在可以使用熟悉的语言（如聊天）与这些模型进行交互，而无需任何技术专业知识或培训。 这些模型是基于提示的——用户发送文本输入（提示）并获取人工智能响应（完成）。 然后，他们可以在多轮对话中迭代地“与人工智能聊天”，完善他们的提示，直到响应符合他们的预期。

“提示”现在成为生成式人工智能应用程序的主要_编程界面_，告诉模型要做什么并影响返回响应的质量。 “提示工程Prompt Engineering”是一个快速发展的研究领域，专注于提示的“设计和优化”，使大规模提供一致且高质量的响应。

## 什么是提示工程?

**提示工程**定义是设计和优化文本输入（提示) 的过程，以便为指定的应用程序目标和模型提供一致且高质量的响应（完成) 。 我们可以将其视为一个两步过程：

- 设计指定模型和目标的初始提示
- 通过迭代的方式提炼提示语以提高响应质量

这必然是一个反复尝试的过程，需要用户的直觉和努力才能获得最佳结果。 那么为什么它很重要呢？ 要回答这个问题，我们首先需要了解三个概念：

- *Tokenization* = 模型如何“看到”提示
- *Base LLMs* = 基础模型如何“处理”提示
- *Instruction-Tuned LLM* = 模型现在如何查看“任务”

### 分词Tokenization

LLM 将提示视为标记序列，其中不同的模型（或模型的版本）可以以不同的方式对同一提示进行标记。 由于 LLM 是根据标记（而不是原始文本）进行训练的，因此提示标记化的方式对生成的响应的质量有直接影响。

要直观地了解标记化的工作原理，请尝试使用如下所示的 [OpenAI Tokenizer](https://platform.openai.com/tokenizer?WT.mc_id=academic-105485-koreyst) 等工具。 复制您的提示 - 并查看如何将其转换为标记token，注意空白字符和标点符号的处理方式。 请注意，此例子显示的是较旧的 LLM (GPT-3) - 因此使用较新的模型尝试此操作可能会产生不同的结果。

![Tokenization](./04_Prompt_Engineering.assets/04-tokenizer-example.pngWT.png)

### 基础模型

一旦一个提示被分词，"基础LLM"（或基础模型）的主要功能就是预测该序列sequence中的标记token。由于LLMs是在大规模文本数据集上训练的，它们对标记之间的统计关系有很好的认识，可以带有一定的置信度confidence进行预测。请注意，它们不理解提示或标记的含义；它们只看到一个它们可以通过下一个预测来“完成”的模式。它们可以继续预测序列，直到被用户干预或满足某些预先建立的条件为止。

但是，如果用户希望看到符合某些标准或任务目标的特定内容，该怎么办？这就是指令调优LLM的用武之地。

### 指令调整

[LLMs 中的指令调整](https://blog.gopenai.com/an-introduction-to-base-and-instruction-tuned-large-language-models-8de102c785a6?WT.mc_id=academic-105485-koreyst) 从基础模型开始，并使用以下参数对其进行微调，可以包含明确指令的示例或输入/输出对（例如多轮“消息”），以及人工智能尝试遵循该指令的响应。

它使用诸如人类反馈强化学习 (Reinforcement Learning with Human Feedback，RLHF) 之类的技术，可以训练模型“遵循指令”并“从反馈中学习”，从而产生更适合实际应用且与用户目标更相关的响应。

## 为什么我们需要提示工程

当前的 LLMs 的算法也有许多挑战，如果不及时优化，就很难实现“可靠且一致的补全”。 例如：

1. **模型响应是随机的。**相同的提示可能会针对不同的模型或模型版本产生不同的响应。 甚至可能在不同时间使用相同模型产生不同的结果。 提示工程技术可以通过提供更好帮助我们最大限度地减少这些变化所带来的影响。
2. **模型可以产生幻觉响应。**模型是使用大型但有限数据集进行预训练的，这意味着它们缺乏有关训练范围之外的概念的知识。 因此，它们可能会产生不准确、虚构或与已知事实直接矛盾的结果。 提示工程技术可以帮助用户识别和减轻幻觉，例如通过向人工智能询问出处或推理过程。
3. **模型功能会有所不同。** 较新的模型或模型迭代将具有更丰富的功能，但也会带来独特的怪癖以及成本和复杂性方面的平衡。 提示工程可以帮助我们开发最佳实践和工作流程，以可扩展和无缝的方式消除差异并适应特定于模型的要求。

- 对相同的 LLM 部署（例如 Azure OpenAI Playground）重复使用相同的提示 - 产生的结果有何不同？

像元提示*metaprompting*和温度配置*temperature configuration*这样的提示工程技术可以在一定程度上减少模型幻觉。 新的提示工程架构还将新工具和技术无缝地融入到提示流程中，以减轻或减少其中一些影响。

## 构建提示的方法

我们已经了解了为什么提示工程重要性 - 现在让我们了解提示是如何构建的，以便我们可以评估针对不同的技术以实现更有效的提示设计。

### 基础提示

让我们从基础提示开始：发送到模型的文本输入，没有其他上下文。 这是一个例子 - 当我们将美国国歌的前几个单词发送到 OpenAI [Completion API](https://platform.openai.com/docs/api-reference/completions?WT.mc_id=academic-105485-koreyst) 时，它会立即完成下一个单词的响应 几行，说明了基本的预测行为。

| Prompt (Input)     | Completion (Output)                                          |
| ------------------ | ------------------------------------------------------------ |
| Oh say can you see | It sounds like you're starting the lyrics to "The Star-Spangled Banner," the national anthem of the United States. The full lyrics are ... |

### 复杂的提示

现在让我们为基础提示添加上下文和说明。 [Chat Completion API](https://learn.microsoft.com/azure/ai-services/openai/how-to/chatgpt?WT.mc_id=academic-105485-koreyst) 让我们可以将复杂的提示构建为集合 messages 包含：

- 反映用户输入和助理响应的输入/输出对。
- 系统消息设置助理行为或个性化。

该请求现在采用以下形式，其中标记化有效地从上下文和对话中捕获相关信息。 现在，更改系统上下文对补全质量的影响并与提供的用户输入一样。

```python
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Who won the world series in 2020?"},
        {"role": "assistant", "content": "The Los Angeles Dodgers won the World Series in 2020."},
        {"role": "user", "content": "Where was it played?"}
    ]
)
```

### 指令式的提示

在上面的示例中，用户提示是一个简单的文本查询，可以解释为信息请求。 通过指令提示，我们可以使用该文本更详细地指定任务，从而为人工智能提供更好的指导。 这是一个例子：

| Prompt (Input)                                               | Completion (Output)                                          | Instruction Type    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------- |
| Write a description of the Civil War                         | *returned a simple paragraph*                                | Simple              |
| Write a description of the Civil War. Provide key dates and events and describe their significance | *returned a paragraph followed by a list of key event dates with descriptions* | Complex             |
| Write a description of the Civil War in 1 paragraph. Provide 3 bullet points with key dates and their significance. Provide 3 more bullet points with key historical figures and their contributions. Return the output as a JSON file | *returns more extensive details in a text box, formatted as JSON that you can copy-paste to a file and validate as needed* | Complex. Formatted. |

## 主要内容

在上面的示例中，提示仍然相当开放，允许 LLMs 决定其预训练数据集的哪一部分是相关的。 使用主要内容设计模式，输入文本分为两部分：

- 指令（动作）
- 相关内容（影响响应）

下面是一个示例，其中的指令是“用 2 句话总结这一点”。

| Prompt (Input)                                               | Completion (Output)                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. It is named after the Roman god Jupiter.[19] When viewed from Earth, Jupiter can be bright enough for its reflected light to cast visible shadows,[20] and is on average the third-brightest natural object in the night sky after the Moon and Venus. <br/>**Summarize this in 2 short sentences** | Jupiter, the fifth planet from the Sun, is the largest in the Solar System and is known for being one of the brightest objects in the night sky. Named after the Roman god Jupiter, it's a gas giant whose mass is two-and-a-half times that of all other planets in the Solar System combined. |

主要内容片段可以通过多种方式使用来驱动更有效的指令：

- **示例** - 不要用明确的指令告诉模型要做什么，而是给它提供要做什么的示例，并让它进行推断。
- **暗示** - 遵循带有“暗示”的说明，引导完成，引导模型做出更相关的响应。
- **模板** - 这些是带有占位符（变量）提示的可重复“配方”，可以使用特定用例的数据进行自定义。

### 用例方式

这种方法中，您使用主要内容来“向模型提供”给定指令所需输出的一些示例，并让它推断所需输出的模式。 根据提供的示例数量，我们可以有零样本提示、单样本提示、少样本提示等。

提示现在由三个部分组成：

- 任务描述
- 所需输出的一些示例
- 新示例的开始（成为隐式任务描述）

| Learning Type | Prompt (Input)                                               | Completion (Output)         |
| :------------ | :----------------------------------------------------------- | :-------------------------- |
| Zero-shot     | "The Sun is Shining". Translate to Spanish                   | "El Sol está brillando".    |
| One-shot      | "The Sun is Shining" => ""El Sol está brillando". <br> "It's a Cold and Windy Day" => | "Es un día frío y ventoso". |
| Few-shot      | The player ran the bases => Baseball  <br/> The player hit an ace => Tennis  <br/> The player hit a six => Cricket  <br/> The player made a slam-dunk => | Basketball                  |

请注意，我们如何必须在零样本提示中提供明确的指令（“翻译为西班牙语”），但它是在一次提示示例中推断出来的。 这个少样本示例展示了如何添加更多示例来让模型在不添加指令的情况下做出更准确的推理。

### 暗示提示

使用主要内容的另一种技术是提供暗示而不是示例。 在这种情况下，我们通过使用反映所需响应格式的片段启动来让模型朝正确的方向进行推理。 然后，模型“接受提示”，继续按照这种思路进行。

| Number of Cues | Prompt (Input)                                               | Completion (Output)                                          |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0              | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. **Summarize This** | Jupiter is the largest planet in our Solar System and the fifth one from the Sun. It is a gas giant with a mass 1/1000th of the Sun's, but it is heavier than all the other planets combined. Ancient civilizations have known about Jupiter for a long time, and it is easily visible in the night sky.. |
| 1              | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. **Summarize This** What we learned is that Jupiter | is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets combined. It is easily visible to the naked eye and has been known since ancient times. |
| 2              | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. **Summarize This** Top 3 Facts We Learned: | 1. Jupiter is the fifth planet from the Sun and the largest in the Solar System. 2. It is a gas giant with a mass one-thousandth that of the Sun... 3. Jupiter has been visible to the naked eye since ancient times ... |

### 提示模版

提示模板是预定义的提示配方，可以根据需要进行存储和重用，以大规模推动更一致的用户体验。 最简单的形式是，它只是一组提示示例的集合，例如 [OpenAI 中的这个例子](https://platform.openai.com/examples?WT.mc_id=academic-105485-koreyst)，它提供了交互式提示组件（用户和系统消息）和 AP驱动请求格式来支持重用。

在它更复杂的形式中，比如[LangChain的这个例子](https://python.langchain.com/docs/modules/model_io/prompts/prompt_templates/?WT.mc_id=academic-105485-koreyst)，它包含占位符，可以替换为来自各种来源的数据(用户 输入、系统上下文、外部数据源等）来动态生成提示。 这使我们能够创建一个可重用的提示库，可用于大规模地**以编程方式**驱动一致的用户体验。

最后，模板的真正价值在于能够为垂直应用程序领域创建和发布提示库 - 其中提示模板现在已优化以反映特定于应用程序的上下文或示例，使响应对于目标用户受众更加相关和准确 。 [Prompts For Edu](https://github.com/microsoft/prompts-for-edu?WT.mc_id=academic-105485-koreyst) repo 是这种方法的一个很好的例子，它为教育领域策划了一个提示库，重点关注课程计划等关键目标， 课程设计、学生辅导等

## 支持内容

如果我们将提示构建视为具有指令（任务) 和目标（主要内容），那么次要内容像我们提供的附加上下文**以某种方式影响输出**。 它可以是调整参数、格式化指令、主题分类法等，可以帮助模型定制其响应以适应所需的用户目标或期望。

例如：给定一个包含课程表中所有可用课程的广泛元数据（名称、描述、级别、元数据标签、讲师等）的课程目录：

- 我们可以定义一条指令来“总结 2023 年秋季课程目录”
- 我们可以使用主要内容来提供所需输出的一些示例
- 我们可以使用次要内容来识别最感兴趣的 5 个“标签”。

现在，该模型可以按照几个示例所示的格式提供摘要 - 但如果结果具有多个标签，它可以优先考虑辅助内容中标识的 5 个标签。

------

## 提示最佳实践

现在我们知道了如何构建提示，我们可以开始思考如何设计它们以反映最佳实践。 我们可以从两部分来思考这个问题——拥有正确的心态和采用正确的技术。

### 提示工程思维

提示工程是一个反复试验的过程，因此请记住三个广泛的指导因素：

1. **领域理解很重要。** 响应准确性和相关性是应用程序或用户操作的与特定领域相关的函数。 运用您的直觉和领域专业知识进一步**定制技术**。 例如，在系统提示中定义特定于某个领域的个性化，或在用户提示中使用特定于某领域的模板。 提供反映特定领域上下文的辅助内容，或使用特定领域的提示和示例来指导模型走向熟悉的使用模式。
2. **模型理解很重要。** 我们知道模型产生的结果本质上是随机的。 但模型实现也可能因它们使用的训练数据集（预先训练的知识）、它们提供的功能（例如，通过 API 或 SDK）以及它们优化的内容类型（例如，代码与图像与文本）。 了解您正在使用的模型的优点和局限性，并利用这些知识来确定任务的优先级或构建针对模型功能进行优化的自定义模板。
3. **迭代和验证很重要。** 模型正在迅速发展，提示工程技术也在迅速发展。 作为领域专家，您可能有其他特定应用程序的背景或标准，这些背景或标准可能不适用于更广泛的社区。 使用提示工程工具和技术“快速启动”提示构建，然后使用您自己的直觉和领域专业知识迭代和验证结果。 记录您的见解并创建一个**知识库**（例如提示库），其他人可以将其用作新的基线，以便将来更快地迭代。

## 最佳实践

现在，让我们看看 [Open AI](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-openai-api) 和[Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/concepts/prompt-engineering#best-practices?WT.mc_id=academic-105485-koreyst) 从业者推荐的常见最佳实践。

| 什么                     | 为什么                                                       |
| ------------------------ | ------------------------------------------------------------ |
| 评估最新的模型。         | 新一代模型可能具有改进的功能和质量 - 但也可能会产生更高的成本。评估它们的影响，然后做出迁移决策。 |
| 分开指令和上下文         | 检查您的模型/提供商是否定义了*分隔符delimiters*，以更清晰地区分指令、主要内容和次要内容。这可以帮助模型更准确地为标记分配权重。 |
| 具体和清晰               | 更多地提供有关所期望的上下文、结果、长度、格式、风格等的详细信息。这将提高响应的质量和一致性。将配方recipes捕捉在可重用的模板中。 |
| 使用描述性语言，使用示例 | 模型可能对“展示和告诉”的方法更有响应。从`zero-shot`方法开始，您可以给它一个指令（但不提供示例），然后尝试`few-shot`作为改进，提供一些所期望输出的示例。使用类比。 |
| 使用线索启动             | 通过提供一些前导词或短语来引导模型朝着期望的结果迈进，作为响应的起点。 |
| 加倍努力                 | 有时您可能需要重复告诉模型。在主要内容之前和之后给出指令，使用指令和提示等。迭代和验证以查看哪种方法有效。 |
| 重要顺序                 | 将信息呈现给模型的顺序可能会影响输出，即使在学习示例中也是如此，这要归功于近期偏见recency bias。尝试不同的选项，看看哪种效果最好。 |
| 给模型一个“出路out”      | 为模型提供一个*备用fallback*完成响应，如果由于任何原因无法完成任务，它可以提供这个响应。这可以降低模型生成虚假或捏造响应的机会。 |

与任何最佳实践一样，记住，*您的效果可能会有所不同*，这取决于模型、任务和领域。将这些作为起点，不断迭代，找出对您最有效的方法。随着新模型和工具的可用性，不断重新评估您的提示工程流程，关注流程可扩展性和响应质量。

