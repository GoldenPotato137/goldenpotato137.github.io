---
title: "pyTorch填坑实录"
date: 2021-03-14 11:51:10
tags: "迁移"
---
<ol>
<li>pytorch: grad can be implicitly created only for scalar outputs:</li>
</ol>
<pre><code class="language-python line-numbers">z.backward(torch.ones_like(x))
</code></pre>
<p>原因：backward必须使用标量来进行</p>
<ol start="2">
<li>python本身看起来数据类型不敏感，但是pytorch极其敏感，int与double不能直接相加，出现想干错误后应查看数据类型：</li>
</ol>
<pre><code class="language-python line-numbers">print(tx.dtype) 
tx=tx.to(dtype=torch.float64)
</code></pre>
<ol start="3">
<li>
<p>torch.nn.functional.Softmax 和 torch.nn.softmax不是一个东西<br />
前者本质是一个函数，用于最后的lost计算，后者是一个神经元节点的定义</p>
</li>
<li>
<p>小心tensor的维度，用xxx.size()查看tensor的维度及范围</p>
</li>
</ol>
<pre><code class="language-python line-numbers">print(out.size())
</code></pre>
