---
theme: seriph
colorSchema: 'dark'
highlighter: shiki
lineNumbers: true
drawings:
  persist: false
transition: slide-left
layout: cover
class: 'text-white'
---

# 实用的 Linux 服务器网络教程

一些学校场景下的 Linux 服务器网络技巧

<div class="flex justify-center gap-16 mt-16">
  <div class="text-center">
    <div class="text-5xl mb-3">🖥️</div>
    <h3 class="text-xl font-bold mb-2">一个联网设备</h3>
    <p class="text-sm text-gray-400">理解端口模型</p>
  </div>
  <div class="text-center">
    <div class="text-5xl mb-3">🔗</div>
    <h3 class="text-xl font-bold mb-2">两台设备连接</h3>
    <p class="text-sm text-gray-400">掌握 SSH 原理</p>
  </div>
  <div class="text-center">
    <div class="text-5xl mb-3">🌍</div>
    <h3 class="text-xl font-bold mb-2">三台设备连接</h3>
    <p class="text-sm text-gray-400">了解内网穿透</p>
  </div>
</div>

认真学习本次分享后，你可以：
<div class="flex justify-center gap-12 mt-4 px-8">
  <div class="text-center flex-1">
    <div class="text-green-400 font-bold mb-2">✓ 理解网络设备和端口的概念</div>
  </div>
  <div class="text-center flex-1">
    <div class="text-green-400 font-bold mb-2">✓ 使学校 Linux 服务器使用外网资源</div>
  </div>
  <div class="text-center flex-1">
    <div class="text-green-400 font-bold mb-2">✓ 通过 FRP 实现内网穿透</div>
  </div>
</div>



---
layout: two-cols
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">1. 一个联网设备的模型</h1>
  <h2 style="color: white !important; margin-bottom: 0;">1.1 端口</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0.25rem;">Ports</p>
</div>


<div class="text-xl pr-8 mt-4" style="color: white; line-height: 2.5;">

<p class="mb-4">首先,我们需要对加入网络的设备有一个大致的模型</p>

<p class="mb-4">无论你的设备是 Linux, macOS or Windows, 在网络上,他们都可以看作是具备 65535 个端口 (ports) 的一种东西。</p>

<p class="mb-4">这些端口和设备之间<span v-after class="font-bold text-yellow-400">双向通信</span>,设备发出/接受的信息都是需要经过某一个端口的。</p>

<p>当然,端口不是物理意义上的一个接口,它只是一种<span v-after class="font-bold text-yellow-400">虚拟的通信标记</span>,用于标记流入/流出的信息来自哪里,便于我们更好地<span v-after class="font-bold text-yellow-400">掌控设备的信息流向</span>。</p>

</div>

::right::

<div class="flex items-center justify-center h-full pl-8">
  <img src="/images/single_device.drawio.svg" style="width: 100%; max-width: 500px;" />
</div>

---
layout: two-cols
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">1. 一个联网设备的模型</h1>
  <h2 style="color: white !important; margin-bottom: 0;">1.2 一些著名的端口</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0.25rem;">Some famous ports</p>
</div>

<div class="text-xl pr-8 mt-4" style="color: white; line-height: 0;">

<p>我们使用的电脑是用来处理信息的，而电脑上运行的<span v-after class="font-bold text-yellow-400">程序就是我们处理信息的工具。</span> </p>

<p>一些端口因为长年累月地用与被一种特定的程序使用，因此它们有了特殊的意义。</p>

<p>所以有的时候我们看到一些端口就知道这个服务器在跑什么程序。<span v-after class="font-bold text-yellow-400">一些恶意病毒也会利用这种惯例去策划攻击。</span></p>

</div>

::right::

<div class="flex items-center justify-center h-full pl-8">
  <img src="/images/famous_ports.drawio.svg" style="width: 100%; max-width: 500px;" />
</div>

---
layout: default
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">2. 将两台设备连接</h1>
  <h2 style="color: white !important; margin-bottom: 0;">2.1 SSH：伟大的连接工具</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0rem;">SSH：The GREAT connection tool</p>
</div>

<div class="flex items-center justify-center mb-2">
  <img src="/images/SSH_introduction.drawio.svg" style="width: 100%; max-width: 750px;" />
</div>


<div class="text-xl pr-2 mt-2" style="color: white;">
  <p>上图展示了网络世界中兼具<span class="font-bold text-yellow-400">私密性、稳定性和普遍性</span>的伟大工具 SSH 的工作方式</p>
  <p>SSH1 -  <span class="font-bold text-green-400"> 创建</span>远程(<span class="font-bold text-purple-400">R</span>emote)端口，服务器使用本地的数据：</p>

  <div class="text-lg -mt-1 -mb-1">
```bash
ssh -R 10808:localhost:7897 -p 9999 yourName@11.222.33.44
```
  </div>

  <p>SSH2 - <span v-after class="font-bold text-green-400"> 创建</span>本地(<span v-after class="font-bold text-purple-400">L</span>ocal)端口，本地使用服务器的数据：</p>

  <div class="text-lg -mt-1 -mb-1">
```bash
ssh -L 8888:localhost:7897 -p 9999 yourName@11.222.33.44
```
  </div>
</div>

---
layout: default
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">2. 将两台设备连接</h1>
  <h2 style="color: white !important; margin-bottom: 0;">2.2 LLM 时代的教程</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0rem;">Tutorials in the LLM Era</p>
</div>

<div class="flex items-center justify-center mb-2">
  <img src="/images/SSH_practice.drawio.svg" style="width: 100%; max-width: 700px;" />
</div>


<div class="text-xl pr-1 mt-0" style="color: white;">
  <p style="margin: 1rem 0;">LLM 时代，我认为为每一种操作系统准备一套教程是毫无意义的</p>
  <p style="margin: 1rem 0;"><span class="font-bold text-yellow-400">我们应该传递思路，而非制造轮子。</span> </p>
  <p style="margin: 1rem 0;">依据与 LLM 的交互结果，将下面指令中的 10808 和 7897 换成实际的端口</p>
</div>
<div class="text-lg -mt-1">
```bash
ssh -R 10808:localhost:7897 -p 9999 yourName@11.222.33.44
```
</div>



---
layout: two-cols
dragPos:
  SSH_howtoWork: 487,106,477,391
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">2. 将两台设备连接</h1>
  <h2 style="color: white !important; margin-bottom: 0;">2.3 SSH 的实际工作模式</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0.25rem;">How SSH actually works </p>
</div>

```bash
ssh -R 10808:localhost:7897 -p 9999 yourName@11.222.33.44
```
<div class="text-xl pr-1 mt-0" style="color: white;">
  <p class="mb-2">实际上，SSH 的运行不是那么简单就连上的，从命令中我们也能看出，似乎还出现了<span class="font-bold text-yellow-400">另外一个端口 9999</span></p>
  <p class="mb-2">9999 端口是和服务器位于<span class="font-bold text-yellow-400">同一网络环境的路由器的端口</span></p>
  <p class="mb-2">路由器将外部网络的访问转化为特定端口的能力，被称为 <span class="font-bold text-yellow-400">NAT</span></p>
  <p class="mb-2">端口 9999 <-> 22 的设置又是另外一个话题了，这些设置主要在路由器上展开，今天我们主要讲电脑设备，这个就不细说了。</p>
</div>
::right::
<div class="flex items-center justify-center h-full pl-8">
  <img v-drag="'SSH_howtoWork'" src="/images/SSH_howtoWork.drawio.svg" style="width: 100%; max-width: 500px;" />
</div>

---
layout: two-cols
dragPos:
  whyTwocannot: 476,86,437,391
---

<div class="mb-8">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">3. 将三台设备连接</h1>
  <h2 style="color: white !important; margin-bottom: 0;">3.1 为什么两台设备不行</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0.25rem;">Why two devices are not enough </p>
</div>

<div class="text-xl pr-1 mt-0" style="color: white;">
  <p class="mb-2">两台设备位于不同的网络环境，其<span class="font-bold text-yellow-400">面临的限制是不同的</span></p>
  <p class="mb-2">对于校园网网络，它的进出是严格的。非校内 IP 发起的连接需要被审核甚至拒绝</p>
  <p class="mb-2">对于家用网络，则公网 IP 可能每日/周进行变动，无法建立起稳定的连接</p>
  <p class="mb-2">因此，我们需要一个<span class="font-bold text-yellow-400">稳定，自由的第三方</span>来作为中继</p>
</div>

::right::
<div class="flex items-center justify-center h-full pl-8">
  <img v-drag="'whyTwocannot'" src="/images/SSH_whyTwocannot.drawio.svg" style="width: 100%; max-width: 500px;" />
</div>

---
layout: default
---

<div class="-mt-4 mb-0">
  <h1 style="color: white !important; margin-bottom: 0.5rem;">3. 将三台设备连接</h1>
  <h2 style="color: white !important; margin-bottom: 0;">3.2 FRP内网穿透的基本架构</h2>
  <p style="color: #9ca3af; font-size: 1.2rem; margin-top: 0rem;">Basic architecture of FRP intranet penetration

</p>
</div>

<div class="flex items-center justify-center mb-2">
  <img src="/images/FRP_howtoWork.drawio.svg" style="width: 100%; max-width: 600px;" />
</div>


<div class="text-xl pr-1 -mt-0" style="color: white;">
```bash
ssh -p 12345 yourName@8.130.xx.xxx #阿里云地址
```

  <p class="mb-2"><span class="font-bold text-yellow-400">自由总是伴随着危险。</span>
FRP 将服务器的入口<span class="font-bold text-yellow-400">直接暴露在公网上</span>，如果配置不当，极易遭受恶意攻击和病毒入侵。</p>
  <p class="-mb-2">我们的服务器最近刚因为我的简单配置遭受过一次攻击，<span class="font-bold text-red-400">这是一个血淋淋的教训</span>。</p>
  <p class="-mb-2">等待半年左右，如果确认系统安全稳定，我会为大家分享<span class="font-bold text-green-400">如何正确配置 FRP 的安全策略</span>。 </p>
</div>