---
title: AI学习笔记
date: 2024-06-05 13:20:46
tags:
---

### 深度学习框架

- TensorFlow：
- Keras
- Caffe
- MXNet
- sklearn

### AI提示语最佳实践

#### 用例方式

- 描述任务
- 提供示例
- 新示例的开始
- 如：我现在想要写一个旅游攻略，需要包括XXX，XXX，XXX，比如去潮汕的攻略是这样的：XXXXXXX。现在请帮我写一份去广州的攻略，开头为：XXXXX。
  - 0样本提示（Zero-shot）："The Sun is Shining". Translate to Spanish
  - 单样本提示（One-shot）：
  "The Sun is Shining" => ""El Sol está brillando".
  "It's a Cold and Windy Day" =>
  - 少样本提示（Few-shot）：
  The player ran the bases => Baseball
  The player hit an ace => Tennis
  The player hit a six => Cricket
  The player made a slam-dunk =>

#### 暗示方式

- 几个示例
  - Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history.
  **Summarize This**
  - Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history.
  **Summarize This**
  **What we learned is that Jupiter**
  - Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history.
  **Summarize This**
  **Top 3 Facts We Learned:**

#### 提示模版

- [OpenAI的提示语示例](https://platform.openai.com/docs/examples)

#### 一些参考原则

- **评估最新大模型的优劣**：New model generations are likely to have improved features and quality - but may also incur higher costs. Evaluate them for impact, then make migration decisions.
- **内容和提示语清晰分段**：Check if your model/provider defines delimiters to distinguish instructions, primary and secondary content more clearly. This can help models assign weights more accurately to tokens.
- **具体且清晰**：Give more details about the desired context, outcome, length, format, style etc. This will improve both the quality and consistency of responses. Capture recipes in reusable templates.
- **使用描述性文字和示例**：Models may respond better to a "show and tell" approach. Start with a zero-shot approach where you give it an instruction (but no examples) then try few-shot as a refinement, providing a few examples of the desired output. Use analogies.
- **使用提示语来快速开始**：Nudge it towards a desired outcome by giving it some leading words or phrases that it can use as a starting point for the response.
- **反复强调**：Sometimes you may need to repeat yourself to the model. Give instructions before and after your primary content, use an instruction and a cue, etc. Iterate & validate to see what works.
- **顺序很重要**：The order in which you present information to the model may impact the output, even in the learning examples, thanks to recency bias. Try different options to see what works best.
- **给模型以反馈**：Give the model a fallback completion response it can provide if it cannot complete the task for any reason. This can reduce chances of models generating false or fabricated responses.

### 参考资料

- [Microsoft's AI for beginners](https://microsoft.github.io/generative-ai-for-beginners)
- 