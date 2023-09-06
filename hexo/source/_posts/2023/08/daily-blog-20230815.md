---
title: outlines - 快速可靠的神经文本生成。
date: 2023-08-15 14:43:37
tags:
---

Outlines 〰 是一个用于神经文本生成的库。您可以将其视为 transformers 库中 generate 方法的更灵活替代品。

Outlines 〰 帮助开发人员引导文本生成，以构建与外部系统具有强大接口的稳健界面。它提供了生成方法，确保输出与正则表达式匹配，或者遵循 JSON 模式。

Outlines 〰 提供了强大的提示原语，将提示与执行逻辑分离，从而实现了少样本生成、ReAct、元提示、代理等简单实现。

Outlines 〰 被设计为一个与更广泛生态系统兼容的库，而非替代品。我们尽可能少地使用抽象，生成可以与控制流、条件语句、自定义 Python 函数以及调用其他库交错进行。

Outlines 〰 与所有模型兼容。它仅通过下一个标记的对数与模型进行交互。它也可以与基于 API 的模型一起使用。

## 主要Features

- 🖍️基于Jinja模板引擎的简单而强大的提示基元
- 🚄引导式生成，包括多项选择、类型约束和动态停止
- ⚡快速的正则引导生成
- 🔥根据JSON模式或Pydantic模型快速生成JSON
- 🐍将完成与循环、条件语句和自定义Python函数交错进行
- 💾生成结果缓存
- 🤗与HuggingFace的transformers模型集成

## 安装

通过python安装

```sh
pip install outlines
```

## 引导式生成

确保包含大型语言模型的系统可靠性的第一步是确保它们的输出与用户定义的代码之间存在明确定义的接口。Outlines 提供了控制语言模型生成的方法，以使其输出更加可预测。

### 提早终止

您可以在找到给定字符后停止生成。

```py
import outlines.text.generate as generate
import outlines.models as models

model = models.transformers("gpt2")
answer = generate.continuation(model, stop=["."])("Tell me a one-sentence joke.")
```

### 多种选择

您可以将完成结果缩减为多个可能性之间的选择。

```py
import outlines.text.generate as generate
import outlines.models as models

model = models.transformers("gpt2")

prompt = labelling("Just awesome", examples)
answer = generate.choice(model, ["Positive", "Negative"])(prompt)
```

### 类型约束

可以指定返回的结果类型：

```py
import outlines.text.generate as generate
import outlines.models as models

model = models.transformers("gpt2")

prompt = "1+1="
answer = generate.integer(model)(prompt)

prompt = "sqrt(2)="
answer = generate.float(model)(prompt)
```

### 高效的正则引导式生成

Outlines 还提供了快速的正则引导生成。实际上，上面的 choice、integer 和 float 函数都在底层使用了正则引导生成：

```py
import outlines.models as models
import outlines.text.generate as generate


model = models.transformers("gpt2-medium")

prompt = "Is 1+1=2? "
unguided = generate.continuation(model, max_tokens=30)(prompt)
guided = generate.regex(model, r"\s*([Yy]es|[Nn]o|[Nn]ever|[Aa]lways)", max_tokens=30)(
    prompt
)

print(unguided)
# Is 1+1=2?
#
# This is probably the most perplexing question.
# As I said in one of my articles describing how
# I call 2 and 1, there isn't

print(guided)
# Is 1+1=2? Always
```

### 高效遵循 Pydantic 模型的 JSON 生成

Outlines 〰 允许引导生成过程，以确保输出符合 JSON 模式或 Pydantic 模型。

```py
from typing import List
from enum import Enum
from pydantic import BaseModel, constr

import outlines.models as models
import outlines.text.generate as generate


class Weapon(str, Enum):
    sword = "sword"
    axe = "axe"
    mace = "mace"
    spear = "spear"
    bow = "bow"
    crossbow = "crossbow"


class Armor(str, Enum):
    leather = "leather"
    chainmail = "chainmail"
    plate = "plate"


class Character(BaseModel):
    name: constr(max_length=10)
    age: int
    armor: Armor
    weapon: Weapon
    strength: int


model = models.transformers("gpt2")
sequence = generate.json(model, Character)("Give me a character description")
print(sequence)
# {
#   "name": "ranbelt",
#   "age": 26,
#   "armor": "chainmail",
#   "weapon": "bow",
#   "strength": 5
# }

parsed = Character.model_validate_json(sequence)
print(parsed)
# name='ranbelt' age=26 armor=<Armor.chainmail: 'chainmail'> weapon=<Weapon.bow: 'bow'> strength=5
```

该方法适用于联合类型、可选类型、数组、嵌套模式等。某些字段约束尚不受支持，但其他所有功能都应该可以正常工作。

### 提示

在纯Python中通过连接字符串编写提示很快变得繁琐：提示构建逻辑与程序的其他部分纠缠在一起，呈现的提示结构也变得模糊不清。Outlines 通过将模板封装在“模板函数”内部，使编写和管理提示变得更加容易。

这些函数可以将提示逻辑与一般程序逻辑清晰地分离开来；它们可以从其他模块和库中导入。

模板函数不需要多余的抽象，它们使用 Jinja2 模板引擎以简洁的方式帮助构建复杂的提示。

```py
import outlines.text as text
import outlines.models as models


examples = [
    ("The food was digusting", "Negative"),
    ("We had a fantastic night", "Positive"),
    ("Recommended", "Positive"),
    ("The waiter was rude", "Negative")
]

@text.prompt
def labelling(to_label, examples):
    """You are a sentiment-labelling assistant.

    {% for example in examples %}
    {{ example[0] }} // {{ example[1] }}
    {% endfor %}
    {{ to_label }} //
    """

model = models.transformers("gpt2")
prompt = labelling("Just awesome", examples)
answer = text.generate.continuation(model, max_tokens=100)(prompt)
```

