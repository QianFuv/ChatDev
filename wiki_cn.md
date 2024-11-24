# Wiki

## 快速开始步骤

### 1. 安装 `ChatDev`:

- 访问 readme 的[快速开始部分](README.md#%EF%B8%8F-quickstart)获取安装说明。

### 2. 使用一条命令开始构建软件:

- **构建您的软件:** 使用以下命令开始构建软件,将 `[description_of_your_idea]` 替换为您的想法描述,将 `[project_name]` 替换为您想要的项目名称:
   ```
   python3 run.py --task "[description_of_your_idea]" --name "[project_name]"
   ```

- 以下是 run.py 的完整参数列表:

    ```commandline
    usage: run.py [-h] [--config CONFIG] [--org ORG] [--task TASK] [--name NAME] [--model MODEL]

    argparse

    optional arguments:
      -h, --help       显示帮助信息并退出
      --config CONFIG  配置名称,用于加载 CompanyConfig/ 下的配置;详见下方 CompanyConfig 部分
      --org ORG       组织名称,您的软件将生成在 WareHouse/name_org_timestamp 目录下
      --task TASK     您的想法提示词
      --name NAME     软件名称,您的软件将生成在 WareHouse/name_org_timestamp 目录下
      --model MODEL   GPT 模型,可选 {'GPT_3_5_TURBO','GPT_4','GPT_4_32K'}
    ```

### 3. 检查您的软件

- 生成的软件位于 ``WareHouse/NAME_ORG_timestamp`` 目录下,包括:
    - 该软件的所有文件和使用手册
    - 开发这个软件的公司的配置文件,包括三个配置 JSON 文件
    - 软件构建过程的完整日志
    - 构建这个软件的提示词
- 以下是一个待办事项软件的示例,位于 ``/WareHouse/todo_THUNLP_20230822165503``:
    ```
    .
    ├── 20230822165503.log # 日志文件
    ├── ChatChainConfig.json # 配置文件
    ├── PhaseConfig.json # 配置文件  
    ├── RoleConfig.json # 配置文件
    ├── todo.prompt # 用户查询提示词
    ├── meta.txt # 软件构建元信息
    ├── main.py # 生成的软件文件
    ├── manual.md # 生成的软件文件
    ├── todo_app.py # 生成的软件文件
    ├── task.py # 生成的软件文件
    └── requirements.txt # 生成的软件文件
    ```
- 通常,您只需要安装依赖并运行 main.py 即可使用软件:
    ```commandline
    cd WareHouse/project_name_DefaultOrganization_timestamp
    pip3 install -r requirements.txt
    python3 main.py
    ```

## 可视化工具

- 您可以启动一个 Flask 应用来获取可视化工具,这是一个用于可视化实时日志、回放日志和 ChatChain 的本地网页演示。
- 实时日志和回放日志的区别在于:前者主要用于调试,可以实时打印代理的对话信息、环境变化以及软件生成过程中的许多额外系统信息,如文件变更和 Git 信息。后者用于回放生成的日志,只打印代理的对话信息。
- 只需运行:
```
python3 visualizer/app.py
```

然后开始通过 ``python3 run.py`` 构建软件,并访问[可视化工具网站](http://127.0.0.1:8000/)查看实时可视化的日志,如:

![demo](misc/demo.png)

- 您还可以访问该页面上的 [ChatChain 可视化工具](http://127.0.0.1:8000/static/chain_visualizer.html),
  上传 ``CompanyConfig/`` 下的任何 ``ChatChainConfig.json`` 来获取该链的可视化效果,如:

![ChatChain 可视化工具](misc/chatchain_vis.png)

- 您也可以访问[对话回放页面](http://127.0.0.1:8000/static/replay.html)来回放软件文件夹中的日志文件
    - 点击 ``File Upload`` 按钮上传日志,然后点击 ``Replay``
    - 回放只显示代理之间的自然语言对话,不包含调试日志。

![回放](misc/replay.gif)

## Docker 启动
- 您可以使用 Docker 来快速且安全地使用 ChatDev。由于 ChatDev 经常创建带有 GUI 的软件并在测试阶段执行它们,您需要一些额外的步骤来允许在 Docker 中执行 GUI 程序。

### 安装 Docker
- 请参考 [Docker 官方网站](https://www.docker.com/get-started/) 安装 Docker。

### 准备主机和 Docker 之间的 GUI 连接
- 以 macOS 为例:
  - 安装 Socat 和 xquartz,安装 xquartz 后可能需要重启计算机
  ```commandline
  brew install socat xquartz
  ```
  - 打开 Xquartz 进入设置,允许来自网络客户端的连接
    - ![xquartz](misc/xquartz.jpg)
  - 在主机计算机上运行以下命令并保持运行:
  ```commandline
   socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"
  ```
  - 在主机计算机上运行以下命令检查您的 ip (inet 的地址):
  ```commandline
  ifconfig en0
  ```

### 构建 Docker 镜像
- 在 ChatDev 文件夹下运行:
    ```commandline
    docker build -t chatdev:latest .
    ```
  这将生成一个 400MB+ 的名为 chatdev 的 Docker 镜像。

### 运行 Docker
- 运行以下命令创建并进入容器:
    ```commandline
    docker run -it -p 8000:8000 -e OPENAI_API_KEY=YOUR_OPENAI_KEY -e DISPLAY=YOUR_IP:0 chatdev:latest
    ```
  ⚠️ 您需要将 ``YOUR_OPENAI_KEY`` 替换为您的密钥,将 ``YOUR_IP`` 替换为您的 inet 地址。
- 然后您就可以运行 ``python3 run.py`` 来使用 ChatDev。
- 您可以先运行 ``python3 visualizer/app.py &`` 启动一个后台程序,这样就可以通过 WebUI 使用在线日志。

### 从 Docker 复制生成的软件
- 运行: 
    ```commandline
    docker cp container_id:/path/in/container /path/on/host
    ```
### 官方 Docker 镜像
- 正在准备中

## 体验式协同学习指南
### 协同追踪

- **开始协同追踪**: 使用以下命令开始构建软件,将 `[description_of_your_idea]` 替换为任务描述,将 `[project_name]` 替换为项目名称。这与启动 ChatDev 相同。
   ```bash 
   python3 run.py --task "[description_of_your_idea]" --name "[project_name]"
   ```
  在协同追踪阶段生成的软件将为后续步骤中代理的经验池做好准备。

### 协同记忆
- **启动协同记忆**: 要开始对指定目录中生成的软件进行记忆过程,使用以下命令运行 `ecl.py` 脚本:
  ```bash
    python3 ecl/ecl.py "<path>" "[options]"
  ```
  `<path>`: 要处理的文件或目录的路径。
  `[options]`: 可以设置为 `-d`。此标志表示脚本应处理给定目录中的所有文件。如果未设置此标志,脚本将处理 path 中指定的文件。
完成此过程后,软件生产过程中提取的经验将被添加到 `ecl/memory/MemoryCards.json` 中的代理经验池中。
\
**例如:**
  如果您只想记忆一个软件,可以使用:
  ```bash
    python3 ecl/ecl.py "<Software Path to file>"
  ```
  软件路径应该类似 `"WareHouse/project_name_DefaultOrganization_timestamp"`。
  \
  如果您想记忆目录中的所有文件,可以使用:
  ```bash
    python3 ecl/ecl.py "<Software Path to Directory>" -d
  ```
  软件路径应该类似 `"WareHouse"`。

- **记忆过滤**: 为了获得更高质量的经验池,建议使用 `ecl/post_process/memory_filter.py` 来过滤 `MemoryCards.json`。运行 `memory_filter.py` 脚本时,您需要指定三个参数:过滤阈值、输入目录和输出目录。
  ```bash
    python3 ecl/post_process/memory_filter.py "<threshold>" "<directory>" "<filtered_directory>"
  ```
  - `<threshold>`: 需要在 0 到 1(不含)范围内的值。用作根据经验的 'valuegain' 进行过滤的阈值。只有 'valuegain' 大于或等于此阈值的经验才会被考虑。
  - `<directory>`: 您打算处理的记忆目录的文件路径。
  - `<filtered_directory>`: 您想要存储处理后数据的目录的文件路径。

  \
  **例如:**
  ```bash
    python3 ecl/post_process/memory_filter.py 0.9 "ecl/memory/MemoryCards.json" "ecl/memory/MemoryCards_filtered.json"
  ```

> **注意:** 默认情况下,`MemoryCards.json` 被设置为空。您可以按照上述步骤为代理自定义您自己的经验池。我们还在 [MemoryCards.json](https://drive.google.com/drive/folders/1czsR4swQyqpoN8zwN0-rSFcTVl68zTDY?usp=sharing) 中提供了我们在实验中使用的 `MemoryCards.json`。您可以通过链接下载 json 文件并将其放在 `ecl/memory` 文件夹下。这允许您直接进入协同推理阶段,而无需重新执行协同追踪和协同记忆步骤。

### 协同推理
- **记忆使用配置**:
  在 `CompanyConfig/Default/ChatChainConfig.json` 文件中,`with_memory` 选项应设置为 **True**。\
  在 `ecl/config.yaml` 文件中,您可以调整代码和文本检索的 **top k** 和 **相似度阈值** 设置。
  默认情况下,`with_memory` 设置为 False,系统配置为检索相似度阈值为零的前 1 个结果(对于代码和文本)。

- **开始协同推理**: 完成记忆使用配置后,与协同追踪阶段类似,您可以使用以下命令开始软件构建过程。用测试集中的任务描述替换 `[description_of_your_idea]`,用测试集中的项目名称替换 `[project_name]`:
   ```
   python3 run.py --task "[description_of_your_idea]" --name "[project_name]"
   ```
   在这个软件开发过程中,代理将把他们的经验池(`MemoryCards.json`)融入到软件开发中!

关于这个**体验式协同学习**模块的详细描述和实验结果请参见我们的预印本论文: https://arxiv.org/abs/2312.17025。

## 体验式协同进化指南
- **使用协同进化**: 使用以下命令启动经验进化,该命令使用 `ecl/ece.py` 来处理 `ecl/memory/UsedMemory.json` 和 `ecl/memory/NewMemory.json`。然后它将这两部分经验组合形成一个新的经验池,存储在 `ecl/memory/Evolved_directory.json` 中。

  ```bash
    python3 ecl/ece.py "<Path_directory>" "<UsedMemory_directory>" "<NewMemory_directory>" "<Evolved_directory>"
  ```
  `<Path_directory>`: 使用 `UsedMemory_directory` 中的记忆生成的软件目录的路径。\
  `<UsedMemory_directory>`: UsedMemory 目录的路径,该记忆用于生成 `Path_directory` 中的软件。\
  `<NewMemory_directory>`: NewMemory 目录的路径,该记忆是使用 `ecl/ecl.py` 从 `Path_directory` 中的软件获得的。\
  `<Evolved_directory>`: 您想要存储进化后记忆的目录的路径。
  \
  **例如:**
  ```bash
    python3 ecl/ece.py "WareHouse" "ecl/memory/UsedMemory.json" "ecl/memory/NewMemory.json" "ecl/memory/MemoryCards_Evolved.json"
  ```
> **注意:** 软件目录和记忆目录必须对应。"<Path_directory>" 中的软件是使用 "<UsedMemory_directory>" 生成的,而 "<NewMemory_directory>" 是从 "<Path_directory>" 中的软件获得的。这是因为当我们计算经验的频率分布时,我们需要确保软件与经验对应,以消除某些经验来获得检索概率相对较高的子集。

关于这个体验式协同进化模块的详细描述和实验结果请参见我们的预印本论文: https://arxiv.org/abs/2405.04219。

## 自定义功能

- 您可以在三种粒度上自定义您的公司:
    - 自定义 ChatChain
    - 自定义 Phase
    - 自定义 Role
- 以下是 ChatDev 的总体架构概览,说明了上述三个类之间的关系:

![arch](misc/arch.png)

- 所有与 ChatDev 相关的配置内容(如代理员工的背景提示词、每个 Phase 的工作内容以及 Phase 如何组合成 ChatChain)都称为 **CompanyConfig**(因为 ChatDev 就像一个虚拟软件公司)。这些 CompanyConfigs 位于 ChatDev 项目的 ``CompanyConfig/`` 目录下。您可以查看这个[目录](https://github.com/OpenBMB/ChatDev/tree/main/CompanyConfig)。在这个目录中,您会看到不同的 CompanyConfig(如 Default、Art、Human)。通常,每个 CompanyConfig 都会包含 3 个配置文件:
  1. ChatChainConfig.json: 控制 ChatDev 的整体开发过程,包括每个步骤是哪个 Phase、每个 Phase 需要循环多少次、是否需要反思等。
  2. PhaseConfig.json: 控制每个 Phase,对应 ChatDev 项目中的 ``chatdev/phase.py`` 或 ``chatdev/composed_phase.py``。Python 文件实现了每个阶段的具体工作逻辑。这里的 JSON 文件包含了每个阶段的配置,如背景提示词、哪些员工参与该阶段等。
  3. RoleConfig.json: 包含每个员工(代理)的配置。目前,它只包含每个员工的背景提示词,这是一串包含占位符的文本。
- 如果某个 CompanyConfig 不包含所有三个配置文件(如 Art 和 Human),这意味着该 CompanyConfig 中缺失的配置文件将按照 Default 设置。目前提供的官方 CompanyConfigs 包括:
  1. Default: 默认配置
  2. Art: 允许 ChatDev 根据需要创建图像文件,自动生成图像描述提示词并调用 OpenAI API 生成图像
  3. Human: 允许人类用户参与 ChatDev 的代码审查过程

### 自定义 ChatChain

- 参见 ``CompanyConfig/Default/ChatChainConfig.json``
- 您可以通过修改 JSON 文件,轻松地从所有阶段(来自 ``chatdev/phase.py`` 或 ``chatdev/composed_phase.py``)中选择和组织阶段来构建 ChatChain

### 自定义 Phase

- 这是唯一需要修改代码的部分,它为自定义提供了很大的灵活性。
- 您只需要:
    - 实现您的阶段类(在最简单的情况下,只需要修改一个函数)继承 ``Phase`` 类
    - 在 ``PhaseConfig.json`` 中配置这个阶段,包括编写阶段提示词和为这个阶段分配角色
- 自定义 SimplePhase
    - 配置请参见 ``CompanyConfig/Default/PhaseConfig.json``,实现您自己的阶段请参见 ``chatdev/phase.py``
    - 每个阶段包含三个步骤:
        - 从整个 ChatChain 环境生成阶段环境
        - 使用阶段环境控制阶段提示词并执行该阶段中角色之间的对话(通常不需要修改)
        - 从对话中获取研讨结论,并用它来更新整个 ChatChain 环境
    - 以下是一个选择软件编程语言的简单阶段示例:
        - 生成阶段环境:我们从 ChatChain 环境中选择任务、模态和想法
        - 执行阶段:无需实现,在 Phase 类中已定义
        - 更新 ChatChain 环境:我们获取研讨结论(选择的语言)并更新 ChatChain 环境中的 'language' 键
          ```python
          class LanguageChoose(Phase):
              def __init__(self, **kwargs):
                  super().__init__(**kwargs)

              def update_phase_env(self, chat_env):
                  self.phase_env.update({"task": chat_env.env_dict['task_prompt'],
                                         "modality": chat_env.env_dict['modality'],
                                         "ideas": chat_env.env_dict['ideas']})

              def update_chat_env(self, chat_env) -> ChatEnv:
                  if len(self.seminar_conclusion) > 0 and "<INFO>" in self.seminar_conclusion:
                      chat_env.env_dict['language'] = self.seminar_conclusion.split("<INFO>")[-1].lower().replace(".", "").strip()
                  elif len(self.seminar_conclusion) > 0:
                      chat_env.env_dict['language'] = self.seminar_conclusion
                  else:
                      chat_env.env_dict['language'] = "Python"
                  return chat_env
          ```
          这个阶段的配置如下:
          ```json
          "LanguageChoose": {
            "assistant_role_name": "Chief Technology Officer",
            "user_role_name": "Chief Executive Officer",
            "phase_prompt": [
              "根据下面列出的新用户任务和一些创意头脑风暴想法: ",
              "任务: \"{task}\".",
              "模态: \"{modality}\".",
              "想法: \"{ideas}\".",
              "我们已决定通过一个用编程语言实现的可执行软件来完成任务。",
              "作为{assistant_role},为了满足新用户的需求并使软件可实现,您应该提出一个具体的编程语言。如果 Python 可以通过 Python 完成此任务,请回答 Python;否则,回答另一个编程语言(例如 Java、C++ 等)。",
              "请注意,我们必须只讨论目标编程语言,不要讨论其他任何内容!一旦我们都表达了自己的意见并一致同意讨论结果,我们中的任何人都必须主动终止讨论,并使用格式:\"{INFO} *\"总结我们讨论的最佳编程语言,其中\"*\"代表一种编程语言,不要添加任何其他文字或原因。"
            ]
          }
          ```

- 自定义 ComposePhase
    - 配置请参见 ``CompanyConfig/Default/ChatChainConfig.json``,实现请参见 ``chatdev/composed_phase.py``。
    - **⚠️ 注意** 我们目前不支持嵌套组合,所以不要在 ComposePhase 中放置 ComposePhase。
    - ComposePhase 包含多个 SimplePhase,并且可以循环执行。
    - ComposePhase 没有 Phase json 文件,但在 chatchain json 文件中您可以定义哪些 SimplePhase 在这个 ComposePhase 中,例如:
      ```json
        {
          "phase": "CodeReview",
          "phaseType": "ComposedPhase",
          "cycleNum": 2,
          "Composition": [
            {
              "phase": "CodeReviewComment",
              "phaseType": "SimplePhase",
              "max_turn_step": -1,
              "need_reflect": "False"
            },
            {
              "phase": "CodeReviewModification",
              "phaseType": "SimplePhase",
              "max_turn_step": -1,
              "need_reflect": "False"
            }
          ]
        }
      ```
    - 您还需要实现自己的 ComposePhase 类,您需要决定阶段环境更新和聊天环境更新(与 SimplePhase 相同,但针对整个 ComposePhase)以及停止循环的条件(可选):
      ```python
      class Test(ComposedPhase):
          def __init__(self, **kwargs):
              super().__init__(**kwargs)

          def update_phase_env(self, chat_env):
              self.phase_env = dict()

          def update_chat_env(self, chat_env):
              return chat_env

          def break_cycle(self, phase_env) -> bool:
              if not phase_env['exist_bugs_flag']:
                  log_visualize(f"**[Test Info]**\n\nAI User (Software Test Engineer):\n测试通过!\n")
                  return True
              else:
                  return False
      ```

### 自定义 Role

- 参见 ``CompanyConfig/Default/RoleConfig.json``
- 您可以使用占位符来使用阶段环境,这与 PhaseConfig.json 相同
- **⚠️ 注意** 您需要在您自己的 ``RoleConfig.json`` 中至少保留 "Chief Executive Officer" 和 "Counselor" 以使反思功能正常工作。

## ChatChain 参数

- *clear_structure*: 是否清除 WareHouse 中的非软件文件和生成的软件路径中的缓存文件。
- *gui_design*: 鼓励 ChatDev 生成带有 GUI 的软件。 
- *git_management*: 是否使用 git 管理生成的软件的创建和更改。
- *incremental_develop*: 是否在现有项目上进行增量开发。
- *self_improve*: 用户输入提示词的自我改进标志。这是 LLM 作为提示词工程师来改进用户输入提示词的特殊对话。**⚠️ 注意** 模型生成的提示词包含不确定性,可能会偏离原始提示词中包含的需求含义。
- *background_prompt*: 将添加到每个 LLM 查询中的背景提示词。
- *with_memory*: 是否利用代理的经验池。经验池实际上位于 `ecl/memory/MemoryCards.json` 中。
- SimplePhase 中的参数:
    - *max_turn_step*: 最大对话轮数。您可以增加 max_turn_step 以获得更好的性能,但完成阶段所需的时间会更长。
    - *need_reflect*: 反思标志。反思是在阶段后自动执行的特殊阶段。它将在辅导员和 CEO 之间开始对话,以完善阶段对话的结论。
- ComposedPhase 中的参数:
    - *cycleNum*: 在此 ComposedPhase 中执行 SimplePhase 的循环次数。

## 项目结构

```commandline
├── CompanyConfig # Configuration Files for ChatDev, including ChatChain, Phase and Role config json.
├── WareHouse # Folder for Generated Software
├── camel # Camel RolePlay Component
├── chatdev # ChatDev Core Code
├── ecl # Experiential Co-Learning Module
├── misc # Assets of Example and Demo
├── visualizer # Visualizer Folder
├── run.py # Entry of ChatDev
├── requirements.txt
├── README.md
└── wiki.md
```

## CompanyConfig

### Default
![demo](misc/ChatChain_Visualization_Default.png)
- 如上图所示的 Default 设置的 ChatChain 可视化,ChatDev 将按以下顺序生产软件:
  - Demand Analysis: 决定软件的模态
  - Language Choose: 选择编程语言
  - Coding: 编写代码
  - CodeCompleteAll: 完成缺失的函数/类
  - CodeReview: 审查并修改代码
  - Test: 运行软件并根据测试报告修改代码
  - EnvironmentDoc: 编写环境文档
  - Manual: 编写使用手册
- 您可以使用 ``python3 run.py --config "Default"`` 来使用默认设置。

### Art
![demo](misc/ChatChain_Visualization_Art.png)
- 与 Default 相比,Art 设置在 CodeCompleteAll 之前添加了一个叫做 Art 的阶段
- Art 阶段会首先讨论图片资源的名称和描述,然后使用 ``openai.Image.create`` 根据描述生成图片。
- 您可以使用 ``python3 run.py --config "Art"`` 或直接忽略 config 参数来使用此设置。

### Human-Agent Interaction
![demo](misc/ChatChain_Visualization_Human.png)
- 与 Default 相比,在***Human-Agent-Interaction***模式下,您可以扮演审查者的角色,要求程序员代理根据您的评论修改代码。
- 它在 CodeReview 阶段之后添加了一个叫做 HumanAgentInteraction 的阶段。
- 您可以使用 ``python3 run.py --config "Human"`` 来使用***Human-Agent-Interaction***设置。
- 当 chatdev 执行到这个阶段时,您会在命令行界面看到请求输入的提示。
- 您可以在 ``WareHouse/`` 中运行您的软件,看看它是否满足您的需求。然后您可以在命令行界面中输入任何内容(bug 修复或新功能),然后按回车键:
![Human_command](misc/Human_command.png)
- 例如:
  - 我们首先运行 ChatDev,任务是"设计一个五子棋游戏"
  - 然后我们在 HumanAgentInteraction 阶段输入"Please add a restart button",添加第一个功能
  - 在 HumanAgentInteraction 的第二个循环中,我们通过输入"Please add a current status bar showing whose turn it is"添加另一个功能
  - 最后,我们通过输入"End"提前退出此模式
  - 以下是所有三个版本。
    - <img src='misc/Human_v1.png' height=250>&nbsp;&nbsp;&nbsp;&nbsp;<img src='misc/Human_v2.png' height=250>&nbsp;&nbsp;&nbsp;&nbsp;<img src='misc/Human_v3.png' height=250>

### Git 模式
- 只需在 ``ChatChainConfig.json`` 中将 ``"git_management"`` 设置为 ``"True"`` 即可开启 Git 模式,在此模式下 ChatDev 会将生成的软件文件夹变成一个 git 仓库并自动进行所有提交。
- 对生成软件代码的每次更改都会创建一个提交,包括:
  - 初始提交,在 ``Coding`` 阶段完成后创建,提交信息为 ``Finish Coding``。
  - 完成 ``ArtIntegration`` 阶段,提交信息为 ``Finish Art Integration``。
  - 完成 ``CodeComplete`` 阶段,提交信息为 ``Code Complete #1/2/3 Finished``(如果 CodeComplete 执行了三个循环)。
  - 完成 ``CodeReviewModification`` 阶段,提交信息为 ``Review #1/2/3 Finished``(如果 CodeReviewModification 执行了三个循环)。
  - 完成 ``CodeReviewHuman`` 阶段,提交信息为 ``Human Review #1/2/3 Finished``(如果 CodeReviewHuman 执行了三个循环)。
  - 完成 ``TestModification`` 阶段,提交信息为 ``Test #1/2/3 Finished``(如果 TestModification 执行了三个循环)。
  - 所有阶段完成,提交信息为 ``Final Version``。
- 在终端和在线日志 UI 中,您可以在过程结束时看到 git 摘要。
  -  <img src='misc/git_summary_terminal.png' height=250>&nbsp;&nbsp;&nbsp;&nbsp;<img src='misc/git_summary_onlinelog.png' height=250>
  - 您还可以在日志文件中搜索 ``git Information`` 来查看提交发生的时间。
- ⚠️ 关于 Git 模式有几点值得注意:
  - ChatDev 是一个 git 项目,我们需要在生成的软件文件夹中创建另一个 git 项目,所以我们使用 ``git submodule`` 来实现这个"git over git"功能。将创建一个 ``.gitmodule`` 文件。
    - 在软件文件夹下,您可以像对待普通 git 项目一样添加/提交/推送/检出软件项目,您的提交不会修改 ChatDev 的 git 历史。
    - 在 ChatDev 文件夹下,新软件已作为一个完整的文件夹添加到 ChatDev 中。
  - 生成的日志文件不会添加到软件 git 项目中,因为日志是在最后一次提交后关闭并移动到软件文件夹的。我们必须这样做,因为日志应该记录所有的 git 提交,包括最后一次。所以 git 操作必须在日志完成之前完成。您总是会看到软件文件夹中有一个日志文件需要添加和提交,如:
    - ![img.png](misc/the_log_left.png)
  - 当您在 ChatDev 项目下执行 ``git add .`` 时,会出现类似以下信息(以五子棋为例):
    ```commandline
    Changes to be committed:
        (use "git restore --staged <file>..." to unstage)
            new file:   .gitmodules
            new file:   WareHouse/Gomoku_GitMode_20231025184031

    Changes not staged for commit:
        (use "git add <file>..." to update what will be committed)
        (use "git restore <file>..." to discard changes in working directory)
        (commit or discard the untracked or modified content in submodules)
            modified:   WareHouse/Gomoku_GitMode_20231025184031 (untracked content)
    ```
    如果您在软件文件夹下添加并提交软件日志文件,就不会有 ``Changes not staged for commit:`` 的提示。
  - 某些阶段的执行可能不会改变代码,因此不会创建提交。例如,软件测试没有问题且没有修改,因此测试阶段不会留下提交。
