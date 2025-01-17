# Paper1. Hyperparamter tuning with Renyi Differential Privacy(使用Renyi差分隐私的超参数调优)

[Hyperparamter tuning with Renyi Differential Privacy .pdf](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/Hyperparamter_tuning_with_Renyi_Differential_Privacy_.pdf)

# 论文概述：

1. 出处： ICLR 2022
2. 作者：Nicolas Papernot∗
Google Research, Brain Team
[papernot@google.com](mailto:papernot@google.com)
Thomas Steinke∗
Google Research, Brain Team
[hyper@thomas-steinke.net](mailto:hyper@thomas-steinke.net)
3. 成就：2022 ICLR 杰出论文
4. 获奖理由：本文对学习算法差分隐私分析的一个重要盲点提供了新的见解，即学习算法在数据上进行多次运行以调优超参数。作者指出，在某些情况下，部分数据可能会扭曲最优超参数，从而泄露私人信息。此外，作者在 Renyi 差分隐私框架下为超参数搜索过程提供了隐私保障。
这是一篇优秀的论文，考虑了学习算法的日常使用及其对社会隐私的影响，并提出了解决方案。这项工作将为差分隐私机器学习算法的后续工作提供基础。

# 论文解读：

1. 解决的问题：对于大部分差分私有算法而言，在对超参数进行调整过程中，会产生需要多次训练而导致的隐私泄漏问题。
2. 解决方式：使用“renyi”差分隐私框架对超参数搜索过程提供隐私保护
3. 结论：调优超参数确实会泄漏隐私。同时在假设泄漏为适度的情况下，只要选择超参数所需的每个候选训练使用Renyi DP，就可以实现更加高效的差异隐私。

# 公式化阐述问题：

## 问题引入与相关公式

- 使用差分隐私方法的思路和公式推导（Dwork et ai）

Def.    不可分性的界限作为隐私的量化标准。对于一个随机化算法M: X n→Y是(ε， δ)-DP，如果对于任何输入X, x0∈X n只在单个记录的添加、删除或替换上不同，对于输出的任何子集S⊆Y，我们有：

![EE866005-F3F1-471C-B69F-66C1BF85DB27.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/EE866005-F3F1-471C-B69F-66C1BF85DB27.png)

作为损失边界，ε越小，提供的隐私保证越强，因为ε越小，提供的隐私保证越强，因为他人很难区分算法在两个相邻输入上的输出。参数δ本质上是保证失效的概率。他人很难区分算法在两个相邻输入上的输出。参数δ本质上是保证失效的概率。

- Renyi差分隐私框架对DP-SGD优化器的改进结论：

![E8C5B2DB-1A2A-4671-95BD-52CFF362BACA.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/E8C5B2DB-1A2A-4671-95BD-52CFF362BACA.png)

其中Dλ的Renyi发散。对于一般的DP—SGD隐私分析，ε应该被认为是一个函数ε(λ)，而不是一个数字。利用高斯噪声加法，使原本的线形边界产生了DP边界的定义。

        利用过去论文得出的DP边界结论，本文经改进后推演得到证明了用对数分布收益(2ε， 0)-差分隐私作为最终结果来替代其原先论文中重复次数的几何分布。同时本文还考虑了重复次数的其他分布。在原先论文给出的一个结果谱上将这些结果扩展到Rényi DP框架，从而产生更清晰的隐私分析。

- 采用SVM软边界的方式考虑使用随机梯度下降算法来最小化损和权重惩罚相应的目标

![5B0A1709-B944-4E51-A730-B2829C7A08B6.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/5B0A1709-B944-4E51-A730-B2829C7A08B6.png)

其中y表示训练样例x∈R2的标签（0，1）。训练集d为标准偏差1.0的各向同性2D高斯数据中提取40个输入。负类从以µ−1 =(7.86，−3.36)为中心的高斯样本中采样，正类在µ1 =(6.42，−9.17)为中心的高斯样本中采样。

       训练过程中的单一超参数α观察性能在有异常值时出现的离群点，并判断是否属于训练的一部分数据。对比使用差分隐私和没有使用差分隐私算法时的区别：

![QQ图片20220725223805.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/QQ%25E5%259B%25BE%25E7%2589%258720220725223805.png)

（黄色代表加了八个异常值的精准度，蓝色则为没加时的精准度）

上述图像表明如果合理调整超参数将有利于减少隐私泄漏的可能。

## 实验与定理

- 定理一（负二项分布）：

       假设我们有m个随机基算法M1, M2，···，Mm: X n→y。这些对应超参数的m种可能设置。

       为了简化符号，我们还假设有一个单一的机制Q: X n→Y，它选择一个随机指标j∈[m]，然后运行Mj。

      首先考虑两种——截断的负二项分布和泊松分布——并陈述我们对这些分布的主要隐私结果。（后续会引入K次结果）

       定义1(截断负二项分布)。设γ∈(0,1)，η∈(−1，∞)。定义N ={1,2，···}上的分布Dη，γ，如下所示。如果η 6= 0, K取自Dη，γ，则：

![92F454F8-7104-46AD-9757-BD32DF56C949.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/92F454F8-7104-46AD-9757-BD32DF56C949.png)

同时，和E [K] = η·(1−γ) γ·(1−γη)。如果K来自于D0，γ，那么：

![A584FBF4-1ED1-40D1-972E-AB5D9FC0B670.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/A584FBF4-1ED1-40D1-972E-AB5D9FC0B670.png)

![D831A339-C464-40AE-9ADC-DC8C396BBB90.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/D831A339-C464-40AE-9ADC-DC8C396BBB90.png)

- 定理二（主要隐私结果-截断负二项式）：

       设Q: X n→Y是一个满足(λ， ε)-RDP和(ˆλ，ˆε)-RDP的随机算法，ˆε≥0，λ∈(1，∞)，ˆλ∈[1，∞]假设Y是完全有序的。

       设η∈(−1，∞)，γ∈(0,1)。定义算法A: xn→Y，如下所示。从截断负二项分布Dη，γ(定义1)中得出K。重复运行Q(x) K次。然后A(x)返回K次运行的最佳值。则A满足(λ， ε0)-RDP，其中：

![2B499EBF-0F74-4735-A4EA-6B6D0C6FF6A8.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/2B499EBF-0F74-4735-A4EA-6B6D0C6FF6A8.png)

![C96B129E-43A0-4BB0-AEC9-7F4FCF997A8B.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/C96B129E-43A0-4BB0-AEC9-7F4FCF997A8B.png)

       上述三张图片分别为：Rényi DP对数分布时的期望重复数与基础算法和naive形成对比；Rényi DP使用不同的分布或naïve组合保证重复，平均值为10，使用0.1-zCDP基础算法；使用不同发行版或naïve组合时，隐私与预期重复次数的对比。Rényi DP保证被转换为近似的DP，绘制ε以便获得(ε， 10−6)-DP。(基本算法为0.1-zCDP)

       定理二主要想阐述隐私与效用之间的权衡

      上述定理推论：

- 推论三（纯粹DP的定理二）：

设Q: xn→Y是一个满足(ε， 0)-DP的随机算法。设η∈(−1，∞)，γ∈(0,1)。定义A: X n→Y，如定理2所示。那么A满足((2 + η)ε， 0)-DP。

- 推论四（集中DP的定理二）：

设Q: xn→Y是一个满足ρ- zcdp -的随机算法，即(λ， ρ·λ)-Rényi DP对于所有λ > 1。设η∈(−1，∞)和γ∈(0,1)。定义A: X n→Y和K←Dη，γ如定理2。假定ρ≤log(1/γ)。对于λ > 1, A满足(λ， ε0)-Rényi DP

![B4A24A11-7B6F-4860-AC7D-43E2A577DE85.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/B4A24A11-7B6F-4860-AC7D-43E2A577DE85.png)

- 备注五：（推论4利用Rényi发散的单调性:如果λ1≤λ2，则Dλ1 (P kQ)≤Dλ2 (P kQ) (V an Erven & Harremos, 2014，定理3)。因此(λ2， ε)-RDP表示任意λ1≤λ2的(λ1， ε)-RDP。特别地，定理2的边界得到ε0→∞为λ→1，因此我们利用单调性来确定λ小的ε0的边界）
- 定理六（利用泊松分布的方式考量截断负二项分布不同的隐私权衡）（主要隐私结果-泊松分布）：

设Q: X n→Y是一个满足(λ， ε)-RDP和(ˆε，ˆδ)-DP的随机算法，对于某些λ∈(1，∞)和ε，ˆε，ˆδ≥0。假设Y是完全有序的。令µ> 0。定义算法A: xn→Y，如下所示。从平均µ-即P [K = K] = e−µ-·µK K的泊松分布中得出K !对于所有k≥0。重复运行Q(x) K次。然后A(x)返回K次运行的最佳值。如果K = 0, A(x)返回一些独立于输入x的任意输出。如果exp(ε)≤1 + 1 λ−1，则A满足(λ， ε0)-RDP，其中：

![91530CDF-EA55-4AC7-AC34-BF961D477F67.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/91530CDF-EA55-4AC7-AC34-BF961D477F67.png)

此定理仅有一个Renyi保证，而之前的定理都有多个Renyi进行保证。

- 引理七（泛界）：

修复λ > 1。设K是N∪{0}上支持的随机变量。设f:[0,1]→R为K -的概率母函数，即f(x):= P∞K =0 P [K = K]·xk。设Q和Q0是Y上的分布，假设Y是完全有序的。定义Y上的分布a，如下所示。首先采样K，然后从Q独立采样K次，输出这些样本中最好的这个输出是a的一个样本，我们类似地用Q0代替q来定义A0，则有：

![0BCAF3C2-72E7-44B6-A1F4-53E9B272E825.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/0BCAF3C2-72E7-44B6-A1F4-53E9B272E825.png)

![38C8DA10-E5EB-45B8-910C-A284DF50B6E9.png](Paper1%20Hyperparamter%20tuning%20with%20Renyi%20Differentia%207dc8336cd4f147f7be45c162fec5770b/38C8DA10-E5EB-45B8-910C-A284DF50B6E9.png)

      上述三张图分别为：重复算法A的期望分位数作为各种分布K的最终隐私保证(ε， 10−6)-DP的函数，其中基本算法Q的每次调用都是0.1-zCDP；重复算法A的最终成功概率(β)作为各种分布的最终隐私保证(ε， 10−6)-DP的函数，其中每次调用基本算法Q有1/100的成功概率，为0.1-zCDP；在超参数搜索结束时得到的CNN模型的精度，考虑了不同的重复次数K的分布，同时报告了500次试验的平均值。

# 阅读完全篇后我对人工智能中“privacy”的理解和整篇感悟反思

- 实际上，在最开始看关于“privacy”的论文时，我感到很不解。我不知道为什么人工智能领域会和“隐私”有关系。因为在我的ML，DL，CV学习过程中，我感觉到我的认识始终停留在如何对已知的信息进行处理，通过各种算法达到最后我么希望的“预测-生成”结果中。经过我在Google上的各种文章搜索以及阅读整篇论文下来，我自认为得到了一个非常通俗的解释：因为在对数据进行算法处理后得到的预测结论会被拿来反推已有的个人信息。（比如一个推荐系统，只要爬取到每个用户的页面，就可以通过一定的反推导算法得到此人的信息，从而信息泄漏）。那么我们需要做的研究就是如何把我们的算法这把“锁”做的更加坚硬，使之无法通过某些算法——“万能钥匙”破解。

       本论文中大量铺成的Renyi算法实际上是在告诉我们如何在一般方法（增加离群数据使其得到有偏差的准确预测度）之外改进算法。同时要在本身模型的accurate上和privacy上做出相应的取舍。（即Renyi DP相较于其他DP算法的优势）

- 在阅读过程中出现了好多我无法理解的生涩算法。在经过查询后，我发现它们都与统计学习方法这本书相关。仅仅只有概率论，数统的基础是不行的。后续也需要自行阅读完毕这本书的内容才能方便我阅读后续的paper
- 本篇论文中设置了很多“前置论文”，导致了我很多知识点仍然没有理解清楚。我决定在后几周的阅读方向主要是差分隐私（本文涉及到的论文）以及CV方向
- 偏学术类型的论文书写规律：开始列出公式，并得到跑出实验的结论。最终的公式推导展现在paper的后面。

# 阅读过程中的一些词汇意义（基础知识点的疏漏）

1.DP-SGD优化器（在过去的论文中提到的算法）

[(三) DP-SGD 算法解释](https://www.cnblogs.com/zhangxianrong/p/15264824.html)

阅读总结：DP-SGD优化器目标是使∀ D 和 D′ ∀ x： ℙ[M(D) = x] ≤ exp( ε ) ⋅ ℙ[M(D′) = x]，通过使用参数梯度来使其实现数据集差分隐私的一种方式。

（后续一定要整理好相关代码，并尝试跑通）

2.SVM的软硬边界

硬边界：使每一个对应的数据点都完美地分布在硬边界的两侧（易造成过拟合，同时常常会使模型更加复杂）

软边界：使分类的错误尽可能小，而不是不出错误。（常见的SVM分类方式，同时对模型的要求不高）

（已经在Deep learning.ai的课上提到过，但是具体的实现代码仍然不是很熟悉）

3.截断负二项式

[负二项分布及其应用](https://zhuanlan.zhihu.com/p/111632687)

阅读总结：

4.naive composition：

[差分隐私（四）- Composition Theorem 组成理论](https://zhuanlan.zhihu.com/p/212819144)

阅读总结：

5.z-CDP算法（具体代码实现）：

[【指数机制代码实现】差分隐私代码实现系列（十）](https://zhuanlan.zhihu.com/p/460704930)

阅读总结：

6.DP是什么？

原现在接触过数据结构中学到过属于“DP”问题里的基础“背包问题”，知道叫做“DP动态规划问题”。经过仔细地搜查资料后，我发现DP算法全称是“多阶段决策过程最优化问题”的一种常用问题，也就是将一个复杂的问题拆分成多个简单的子问题。我们的目标就是要解决“子问题”

7.上述问题全都可以归结为“差分隐私”领域：在算法内的关键点处添加噪声的随机算法。

8.运算符号：“||”：逻辑或