# llama.cpp


[![Actions Status](https://github.com/ggerganov/llama.cpp/workflows/CI/badge.svg)](https://github.com/ggerganov/llama.cpp/actions)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT)

这是一个 C++ 实现的 [LLaMA](https://arxiv.org/abs/2302.13971) 对话库。Java 和 Python的同学也不要被吓倒，它的使用超级简单。如果你遇到什么问题，欢迎到公众号（xjjdog）提问。

**热门问题:**

- 本仓库只是一点点代码。想要完整运行，需要 [下载模型](https://github.com/ggerganov/llama.cpp#instruction-mode-with-alpaca)。
- 输出性能优化: https://github.com/ggerganov/llama.cpp/issues/64
- 创建一个 `llama.cpp` logo: https://github.com/ggerganov/llama.cpp/issues/105

## 描述

和ChatGPT对比起来，llama的好处是：使用普通的Macbook，Linux，甚至Docker、树莓派等，就可以运行类比于 ChatGPT 的对话模型。


- 纯C++代码，代码少，而且没有任何依赖
- Apple 的M1 芯片也可以跑，而且有性能优化
- x86架构拥有 AVX2 支持
- 在 CPU 上就能跑，不需要 GPU

支持的平台:

- [X] Mac OS
- [X] Linux
- [X] Windows (via CMake)
- [X] Docker



模型下载地址：

```
curl -o ggml-alpaca-7b-q4.bin -C - https://gateway.estuary.tech/gw/ipfs/QmQ1bf2BTnYxq73MFJWu1B7bQ2UD6qG7D7YDCxhTndVkPC
curl -o ggml-alpaca-7b-q4.bin -C - https://ipfs.io/ipfs/QmQ1bf2BTnYxq73MFJWu1B7bQ2UD6qG7D7YDCxhTndVkPC
curl -o ggml-alpaca-7b-q4.bin -C - https://cloudflare-ipfs.com/ipfs/QmQ1bf2BTnYxq73MFJWu1B7bQ2UD6qG7D7YDCxhTndVkPC
```

---

那么，这个工具要怎么用呢？ 超级简单。

首先，将代码clone到本地。

```bash
git clone https://github.com/ggerganov/llama.cpp.git
```

然后，进入到llama.cpp目录。

```bash
cd llama.cpp
```

编译代码。

```bash
make
```

生成后的文件名称叫做`main`，以后，我们只需要运行 `./main`即可。

最重要的一步，你需要下载一个数据模型。否则 llama 是不知道加载什么数据来进行计算的。为了测试，我们下载一个最小的。这个文件大小有3.9G，你需要相应大小的内存预留。

```bash
curl -o ggml-alpaca-7b-q4.bin -C - https://gateway.estuary.tech/gw/ipfs/QmQ1bf2BTnYxq73MFJWu1B7bQ2UD6qG7D7YDCxhTndVkPC
```

最后，我们就可以指定这个模型，来进行对话输出了。

```java
./main -m ./ggml-alpaca-7b-q4.bin -p "Will the future be female?" -n 512 --color
```

- m 指定的是模型的位置。
- p 是对话或者问题。比如这里，我问我是否能够吃狗肉!
- n 指定的是输出的文字数量，默认是128。
- --color 输出彩色内容。


下面是一些输出。首先会将输入进行切分，然后生成内容，最后将耗时打印。

```java
% ./main -m ./ggml-alpaca-7b-q4.bin -p "Can i eat dog?" -n 512 --color

No you cannot! Eating dogs is illegal and against the law. It would be considered animal abuse, so please don’t do it under any circumstances…unless you are a cannibal

main: mem per token = 14368644 bytes
main:     load time =   743.12 ms
main:   sample time =   455.50 ms
main:  predict time = 46903.35 ms / 91.79 ms per token
main:    total time = 48455.85 ms
```

在Macbook Pro M1 上运行的视频 [whisper.cpp](https://github.com/ggerganov/whisper.cpp)。

https://user-images.githubusercontent.com/1991296/224442907-7693d4be-acaa-4e01-8b4f-add84093ffff.mp4

## 交互模式

如果你想要和ChatGPT一样有对话能力的话，也是可以的。需要加上 `-i` 参数，当然，也可以使用 `-r User:`参数输出一个提示符。

比如：

```java
./main -m ./ggml-alpaca-7b-q4.bin -p "Will the future be female?" -n 128 --color -i -r "User:"
```

## 授课模式

所谓授课模式，就是提供一个按照顺序输出的文件列表，让电脑按照顺序把答案输出。
如果liyanhong使用这种模式，而不是ppt，估计效果会更好。

比如：

```bash
./main -m ./models/13B/ggml-model-q4_0.bin -n 256 --repeat_penalty 1.0 --color -i -r "User:" -f prompts/chat-with-bob.txt
```

## 内存需求

内存的需求取决于你使用的模型。我们的测试使用的都是最简单的模型，所以4GB就够了。如果想要更精细的输出，你的内存需要更大一些。

| model | original size | quantized size (4-bit) |
|-------|---------------|------------------------|
| 7B    | 13 GB         | 3.9 GB                 |
| 13B   | 24 GB         | 7.8 GB                 |
| 30B   | 60 GB         | 19.5 GB                |
| 65B   | 120 GB        | 38.5 GB                |

## Android

你甚至可以在Android上跑起来。如果你的内存够大，那么完全可以做一个小型对话机器人，还是本地的！

后面如果解决了部分加载的问题，Android的嵌入式应用会非常方便。


更多的内容，可以参考 https://github.com/ggerganov/llama.cpp

## Limitations

- We don't know yet how much the quantization affects the quality of the generated text
- Probably the token sampling can be improved
- The Accelerate framework is actually currently unused since I found that for tensor shapes typical for the Decoder,
  there is no benefit compared to the ARM_NEON intrinsics implementation. Of course, it's possible that I simply don't
  know how to utilize it properly. But in any case, you can even disable it with `LLAMA_NO_ACCELERATE=1 make` and the
  performance will be the same, since no BLAS calls are invoked by the current implementation

### Contributing

- Contributors can open PRs
- Collaborators can push to branches in the `llama.cpp` repo and merge PRs into the `master` branch
- Collaborators will be invited based on contributions
- Any help with managing issues and PRs is very appreciated!
- Make sure to read this: [Inference at the edge](https://github.com/ggerganov/llama.cpp/discussions/205)

### Coding guidelines

- Avoid adding third-party dependencies, extra files, extra headers, etc.
- Always consider cross-compatibility with other operating systems and architectures
- Avoid fancy looking modern STL constructs, use basic `for` loops, avoid templates, keep it simple
- There are no strict rules for the code style, but try to follow the patterns in the code (indentation, spaces, etc.). Vertical alignment makes things more readable and easier to batch edit
- Clean-up any trailing whitespaces, use 4 spaces indentation, brackets on same line, `void * ptr`, `int & a`
- See [good first issues](https://github.com/ggerganov/llama.cpp/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) for tasks suitable for first contributions
