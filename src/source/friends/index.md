---
layout: friends # 必须
title: 我的朋友们 # 可选，这是友链页的标题
---

如你所见，这个界面很空，因为我是个没什么朋友的死肥宅（悲

欢迎朋友们与我互换友链~

具体信息请参考本文末尾。

<!-- more -->

~~一场史无前例的大浩劫已然降临~~

（其实就是搬迁博客的时候才发现wordpress导出的xml文件竟然不包含友链，服务器又早已因为过期惨遭削除了）（悲

## 成为朋友！

欢迎新朋友将你的信息类通过[邮件](mailto:goldenpotato137@gmail.com)发送，我看到邮件后会立刻与你签订契约，成为马猴烧酒（bushi

```cs
public interface Info
{
    public string Name();       // 你的昵称
    public string Title();      // 网站标题
    public string Url();        // 网站超链接
    public string AvatarUrl();  // 头像
    public string? Des();       // 你的描述 （可选）
}
```

以下为我的信息~
```cs
public GoldenPotatoInfo : Info
{
    public string Name() => "GoldenPotato137";
    public string Title() => "GoldenPotato137的小屋";
    public string Url() => "https://potatox.moe";
    public string AvatarUrl() => "https://s3.bmp.ovh/imgs/2024/01/01/218717ae3abfb558.png";
    public string Des() => "HITer/LLer/前ACMer/废萌gal玩家";
}
```
