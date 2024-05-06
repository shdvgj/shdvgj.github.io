---
title: 当前我对于生成式AI的思考
date: 2024-04-28 16:39:34
tags:
---

## 生成式AI发展现状

主要参考自：

- [Generative AI Global Interest Report 2023](https://www.electronicshub.org/generative-ai-global-interest-report-2023/)
- [The state of AI in 2023: Generative AI’s breakout year](https://www.mckinsey.com/capabilities/quantumblack/our-insights/the-state-of-ai-in-2023-generative-ais-breakout-year)

基本都是来自2023年的数据，总结一下有以下几点：

### 主流AI工具使用情况

由于统计口径的原因，这部分数据不包含中国大陆

- 文本AI：主要是使用Quillbot和ChatGPT。使用人数来说，来自菲律宾的使用者最多，每10万人中有5266人使用过，其次是新加坡，每十万人有2673人使用，后面是加拿大，阿联酋，马来西亚等，基本都在每十万人1000多人使用量。不过这个数据相信申请过ChatGPT的国内用户都能心知肚明，申请ChatGPT不能使用国内手机号，基本上都得通过买一个虚拟号码来申请，而最便宜的虚拟手机号基本就在上述提到的几个国家。（2024年的当下已经不需要申请手机号了）
- 图片AI：主要是Midjourney和Dall-E。使用人数来说，以色列最多，每十万人有311个人使用，接下来就是新加坡，芬兰。Midjourney的申请应该是没有ChatGPT那么麻烦，看得出来少了一些奇怪的亚洲国家。
- 音频AI：主要是FakeYou和VoiceGPT。乌克兰使用人数最多，每十万人有230人，接下来是智利，阿根廷，秘鲁这些南美国家。这数据颇有点奇怪，为啥这里变成了南美国家霸榜。怀疑是不是也跟这些音频AI的申请限制有关，大量的国内玩家使用了南美账号申请。
- 视频AI：2023年这个节点还没有sora，还没有主流的视频AI工具，因此使用人数较少。最多的是新加坡，每十万人中有57人使用。接下来是阿联酋，塞浦路斯，加拿大等。
![2023年AI报告](AI-report-2023.png)
<!-- more -->
![most-popular-AI-tool.png](most-popular-AI-tool.png)

### 行业使用情况

后面所有的数据都来自[McKinsey Global Surveys](https://www.mckinsey.com/featured-insights/mckinsey-global-surveys)

- 按照行业类别划分来看，科技媒体行业使用比例最高，**至少使用过一次生成式AI**的比例高达88%，这个也是意料之中。其次是先进产业（Advanced Industries），这个比例是80%。接着是医疗行业，商业法律等领域。
![ai-use-by-industry.png](ai-use-by-industry.png)
- 以地区划分，大中华区比例最高，至少使用一次的比例是83%，不得不说还是中国人卷啊。其次是亚太地区，欧洲和北美。这个图表很有意思，剩下的区域被标识为发展中地区（Developing markets），至少在这个调查中，研究人员认为中国不算是发展中地区了。
![ai-use-by-location.png](ai-use-by-location.png)
- 以管理者职级划分，公司高管级别（C-suite executives）使用过AI的比例是80%。剩下的中高级管理者这个比例也没差太多。
![ai-use-by-age.png](ai-use-by-age.png)
- 以年龄划分，这个倒是没什么意外，年纪越小，使用的比例越高。
![ai-use-by-title.png](ai-use-by-title.png)
- 男女划分来说，使用过AI的比例差不多，值得注意的差别是深入使用AI（工作和生活中多次使用）的比例，男性达到40%，女性是33%。
![ai-use-by-gender.png](ai-use-by-gender.png)

### 实际解决行业问题情况

下面指的是在实际工作中通过生成式AI解决问题的比例

- 解决市场和营销相关问题的比例最高，达到14%，其次是产品和服务开发（13%），然后是实施（10%），排最后的是手工业，只有2%。
- 实际解决方式中，以生成文案初稿（9%）最高，其次是个性化营销（8%），文章总结，消费者个性化趋势等。
![ai-use-by-function.png](ai-use-by-function.png)

### 使用AI的风险考虑

- 不准确性（56%）、网络安全（53%）和知识产权侵权（46%）是生成式人工智能采用中最常被提到的风险。
- 此外提到了可解释性（Explainability）这个风险，也就是人工智能的决策可能无法提供详细的解释，导致让人很难信任。
![ai-use-risk.svg](ai-use-risk.svg)

### 使用AI的主要目的

- AI高产值公司更倾向于利用AI提升业绩（27%）和创造新的业务收入（23%）。
- 其他公司更倾向于降低核心业务的成本（33%）。
![ai-use-objective.png](ai-use-objective.png)

### AI对于工作内容的影响

- AI相关职位招聘难度相比2022年都有所下降，显然是因为AI相关从业人员增加了
![ai-use-hiring.svg](ai-use-hiring.svg)
- 对于AI是否降低工作的内容，提高工作效率的方面
![ai-use-workforce.svg](ai-use-workforce.svg)
- 对于AI提升工作效率的期望方面
![ai-use-workforce-expect.svg](ai-use-workforce-expect.svg)

## 我的一点思考

### 惊艳

AIGC刚出世的时候，确实惊艳世人。我也必须得承认被ChatGPT的能力完全震惊了。也导致我刚离职的一段时间，花了极大的热情去做接入ChatGPT接口的项目。

不仅仅是我，数不清的自媒体，公众号，小程序，网页如雨后春笋般全都套着AI的主题，出现在公众的视野里。我也在开发了一俩月的小程序后立刻发现，这个赛道已经挤满了人，我开发的项目根本没办法有什么竞争力。

那段时间也是我最感到焦虑的时候，被铺天盖地的“AI取代程序员”的言论洗脑，加上刚被裁员，着实感到我的未来非常暗淡，一度考虑是不是要转行。

如果经历过那段时期，应该能明白我的感受。当时的互联网充斥着这么些内容：

- 用ChatGPT写故事书，上架电子书网站大卖数万本。
- ChatGPT + Midjourney，在图片素材网站上被打赏好几万。
- 说一段指令，ChatGPT瞬间写出一段可运行的程序或者游戏。
- 甚至有人创造了一个完全由人工智能组成的虚拟软件公司，这些人工智能还成功开发出了软件。

你能感觉到，这些内容充斥着人工智能要取代掉你的暗示。如果“贩卖焦虑”这个词我以前还没怎么有概念的话，现在我是切实感受到了：我尝试做独立开发好久都没听个响，这些人依靠人工智能这么轻松的就能挣到那么多？

讽刺的是，现在回想，这段时间OpenAI都还没挣到钱，而这些通过“贩卖焦虑”的Youtuber们，自媒体，培训机构却着实是赚了个盆满钵满。

### 厌倦

ChatGPT的横空出世之后长达一年的时间里，这股AI热潮一直在高歌猛进。无论国内外都是在使劲冲刺，几乎所有稍具规模的科技公司都有自家的大模型，包括谷歌的Bard

### 质疑

// TODO