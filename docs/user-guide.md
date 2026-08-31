# ReCo — Research Cosmos

### User Guide · v0.0.2

---

## What is ReCo?

**ReCo (Research Cosmos)** is a **self-configuring and self-evolving agentic system for
biomedical research** — spanning radiology and medical physics, biology, biomedical
engineering, and beyond. You describe what you want in plain language, and an autonomous
agent carries it out — reading and writing files, running code, and calling specialized
research tools on your behalf. ReCo is not locked to any one domain or toolset:
it grows to fit whatever you work on.

ReCo ships with a curated set of **pre-configured tools** (MCP servers) covering common
research tasks: segmentation, radiomics, tabular and image classification, AutoML,
exploratory data analysis, and more. These ready-made tools let you get productive
immediately.

But the predefined tools are only the starting point. **ReCo's defining purpose is that
it is open-ended and extensible:** on your request, the agent can wrap *any* framework
you point it to — a GitHub repository, a local project, or a Python package — and turn it
into a new tool inside ReCo. In other words, the bundled servers are examples of what ReCo
can do, not the limit of it. Over time you can grow ReCo into a personal research cosmos
tailored to your own workflows.

This guide walks through the first-run setup and the main panels of the app, step by step.

---

## Step 1 — Choose and configure your model

Everything in ReCo is driven by a language model (LLM). Before anything else, open the
**Settings** panel and tell ReCo which model to use.

![Settings panel — provider, account, model, thinking level, approvals, and sandbox](images/01-settings.png)

**Provider and model.** Pick a **Provider** from the dropdown, then a **Model**. ReCo
supports:

- **OpenAI** — the recommended option and the core reasoning engine for the full ReCo
  experience. Sign in with an eligible **ChatGPT subscription**, or use an **OpenAI
  Platform API key**, to access OpenAI's state-of-the-art GPT models through Codex. The
  model list is discovered from the signed-in account and installed Codex runtime, so it
  always reflects what is actually available to you. In v0.0.2, the current GPT-5.6
  family includes **GPT-5.6 Sol** (the default and most capable choice), **GPT-5.6 Terra**
  (a balanced choice), and **GPT-5.6 Luna** (the efficient choice), subject to account
  availability. See https://developers.openai.com/api/docs/models
- **Ollama (Local)** — open models running entirely on your own machine, for fully
  offline use and maximum data privacy. No API key needed; install Ollama from
  https://ollama.com/download
- **Ollama Cloud** — hosted Ollama models. Create an API key at
  https://ollama.com/settings/keys
- **OpenAI-compatible** — any other provider that accepts an API key and implements the
  OpenAI **Responses API**. Enter its provider name, base URL, exact model ID, and API
  key.

**OpenAI authentication.** You have two options:

1. **ChatGPT subscription login** — click **Sign in with ChatGPT** and complete the browser
   login. ReCo then uses the access available through your eligible ChatGPT plan, with no
   separate API key required, or
2. **OpenAI Platform API key** — paste a key from https://platform.openai.com/api-keys and
   click **Use API key**. API usage is billed separately from a ChatGPT subscription.

For details on these two Codex authentication paths, see
https://learn.chatgpt.com/docs/auth. Credentials for Ollama Cloud and other compatible
providers are kept in protected local storage when the operating-system keychain is
available.

**Thinking level.** Select how much reasoning effort the model should use. For OpenAI,
ReCo shows only the thinking levels supported by the selected model. Other providers
receive the chosen level only when their Responses API implementation supports it.

> **Model compatibility:** ReCo's complete agentic workflow — including Codex tool
> semantics, MCP tool discovery and invocation, approvals, and skill-driven extension —
> is developed and tested around the **Codex app-server protocol with OpenAI models**.
> Ollama and other OpenAI-compatible models can still be useful for chatting and coding-agent
> tasks, but support for Codex's namespaced MCP tools and tool-selection behavior varies by
> provider and model. For the most reliable use of the bundled MCP servers and ReCo's
> self-configuring and self-evolving capabilities, use an OpenAI model through Codex.

> **Tip — data privacy:** If you work with patient-sensitive data, be aware that with a
> hosted provider, any file content the agent reads (DICOM metadata, CSV/Excel records) is
> sent through the API to that provider. It is strongly advised to work with **anonymized
> data**, or to use **local LLMs (Ollama Local)** if your equipment allows it.

**Set approvals and access.** Under **Approvals**, we recommend **Ask when needed** so
ReCo asks before shell commands and file changes that require your authorization. Under
**Sandbox**, choose **Read only**, **Workspace write** (the recommended balance), or
**Full filesystem access**. You can separately allow network access for shell commands
and enable or disable web search. These controls determine what ReCo may do on your
machine while it works.

---

## Step 2 — Skills

**Skills** are domain-specific instruction sets that the agent loads on demand to perform
specialized, multi-step procedures correctly. Open the **Skills** panel to see them.

![Skills panel — built-in skills, all enabled, with the option to import your own](images/02-skills.png)

ReCo includes three built-in skills, all **enabled** by default:

- **install-mcp-from-repo** — installs a brand-new MCP server into ReCo from a Git URL or
  a local source folder. This is the mechanism that lets you extend ReCo with **any**
  framework: the skill studies how the bundled servers are built and mirrors those
  patterns when wrapping your new source.
- **regenerate-server-docs** — rewrites the documentation for a user-installed server
  after its tools change.
- **setup-python-environments** — the one-time first-install routine that sets up Python,
  the conda environments, and the bundled servers (used in Step 3).

You are not limited to the built-in skills. To **import your own skill**, click
**Open skills folder** and drop in a folder containing a `SKILL.md` file, then click
**Refresh**. Your custom skill appears under **User-installed** and becomes available to
the agent.

---

## Step 3 — Set up the system and run your first task

Once your LLM is selected and configured, you do a **one-time system setup** so that the
pre-included MCP servers become usable.

### 3a. First-launch setup

Go to the **Chat** panel and start a new chat with a simple prompt such as:

> *"This is the first launch of the app. Please set it up."*

The agent will then, with your confirmation at the heavy step:

1. **Check for Conda** and install Miniconda if it isn't already present.
2. **Create 5 Conda environments**, grouped by dependency compatibility, and install all
   the required packages into them (this is a large download, roughly 5–10 GB).
3. **Configure all the MCP servers** so that ReCo's **10 pre-included tools** are wired up
   and ready to run.

This step runs once. When it finishes, the bundled servers are installed and configured —
but, by design, **not yet exposed to the agent**.

### 3b. Enable the server you need

To expose a tool to the agent, open the **Servers** panel and toggle on the server you
want to use. The 10 bundled servers are:

| Server | What it does |
| --- | --- |
| **TotalSegmentator** | Segment 171 anatomical structures (organs, bones, vessels, muscles) in CT/MR. |
| **nnU-Net Segmentation Toolkit** | End-to-end nnU-Net v2: prepare data, preprocess, train, and predict segmentations. |
| **PyRadiomics Feature Extraction** | Extract radiomic features (first-order, shape, GLCM, GLRLM, GLSZM, GLDM, NGTDM). |
| **Medical Image Classification (2D)** | PyTorch 2D classification — ResNet, VGG, Inception, EfficientNetV2, fine-tuning. |
| **Medical Image Classification (3D)** | PyTorch + MONAI 3D classification — 3D ResNet/DenseNet and fine-tuning. |
| **Classification Model Performance Comparison** | Compare training vs. inference metrics to detect performance degradation. |
| **PyCaret Classification** | Tabular classification AutoML — compare, tune, blend, evaluate, infer. |
| **PyCaret Regression** | Tabular regression AutoML — compare, tune, blend, evaluate, infer. |
| **Feature Importance & Selection** | Tabular feature importance/selection (Random Forest, F-test, mutual info, RFE). |
| **EDA Analysis** | Exploratory data analysis with publication-quality plots and statistical tests. |

![Servers panel — toggle TotalSegmentator on to expose it to the agent](images/03-servers.png)

### 3c. Run a task

After enabling a server, open a **new chat** and make your request. For example, to
segment anatomy in a CT scan:

1. Enable the **TotalSegmentator** server (as above).
2. Start a new chat and provide the **path to the CT scan** — either by typing it, or by
   using the dedicated **file input button** to browse and select it.
3. Ask the agent in plain language, e.g. *"Segment the liver, spleen, and kidneys in this
   CT and save the masks."*

The agent sees the exposed TotalSegmentator tool, calls it with the right parameters, and
fulfils your task — returning the segmented masks. The same pattern applies to every
server: enable it, point the agent at your data, and describe the goal.

---

## The heart of ReCo — self-configuring & self-evolving

This is the single most important capability of ReCo, and the reason it is more than a
collection of pre-built tools: **ReCo can extend itself on demand.** When you point it at
a framework it doesn't yet have, the agent reads that framework's source, learns how it
works, builds a new MCP server that wraps it, and registers it inside the app — turning an
arbitrary external project into a first-class ReCo tool. The 10 bundled servers were
created the same way; you can keep growing the app indefinitely.

The whole process is driven by the built-in **`install-mcp-from-repo`** skill (see
*Step 2 — Skills*), and you supervise it at every important decision.

### Recommended workflow

For clean, well-organized results, follow this order:

1. **Create a project** for the new framework and **name it accordingly** (e.g. *"Merlin"*).
   See *The Projects panel* below.
2. **Activate** that project (**Make active**).
3. **Open a new chat** and **request the add-on** of the new framework.
4. Let the agent **install and configure** the new server (described step by step below).
5. When it finishes, ask the agent to **update the project's memory**, so it has a durable
   record of what it built for future reference.
6. **Enable the new server** in the **Servers** panel.
7. **Open a new chat** (with the same project still active, so the chat is filed under it)
   and **start working** with your new capability.

### What happens during installation

**1. You request the framework.** In a chat, point ReCo at a Git URL (or a local folder)
and ask it to add the framework to its capabilities. The agent recognizes the request and
launches the `install-mcp-from-repo` skill.

![Requesting a new framework — the agent launches the install-mcp-from-repo skill](images/07-import-request.png)

**2. The agent presents an install plan and asks permission.** Before touching your
machine, ReCo explains exactly what it intends to do — clone the repo into ReCo's
user-data area, create a dedicated Conda environment, install the framework's
dependencies, study the bundled servers to follow the same patterns, generate a server
wrapper, and register the new server (disabled by default). It also shows a **security
note** — installing from a repository runs code from that source, so only proceed with
sources you trust. Nothing happens until you confirm.

![The agent lays out the install plan and asks to proceed; you approve](images/08-install-plan.png)

**3. The agent designs the tool surface and confirms it with you.** After studying the
source, ReCo proposes the concrete set of tools the new server will expose — their names,
whether each is *fast* or *long-running* (background job), and their parameters — and asks
you to confirm or revise. This is where you shape how the framework appears inside ReCo.

![The agent proposes the tool surface (tools, parameters) and asks for confirmation](images/09-tool-surface.png)

**4. The new server appears in the Servers panel.** Once installation completes, the new
server shows up in **Servers**, tagged **USER**, alongside the bundled ones. Each card
shows its Python interpreter, the size of any pretrained models, a **Docs** button, and a
**Delete** button. **Toggle it on** to expose it to the agent.

![The newly installed server (e.g. Merlin) in the Servers panel, ready to enable](images/10-installed-server.png)

Then open a new chat (with your project active) and begin working with the new tools.

> **Note — first run downloads models.** Frameworks that rely on **pretrained models** —
> such as **Merlin**, **MAISI**, **TotalSegmentator**, and many others — download and
> cache those model weights on their **first run**. That first invocation can therefore
> take a while (the weights may be several GB). Subsequent runs reuse the cached models
> and are much faster.

---

## The Projects panel

A **project** groups related chats and keeps context across them. We strongly recommend
working inside a project.

![Projects panel — create a project, make it active, and manage its memory](images/04-projects.png)

- **Create a project:** click **+ New project** and give it a name.
- **Make it active:** select the project and click **Make active**. From then on, every
  new chat is saved under that project.
- **Project memory:** each project has a Markdown **memory file** that ReCo injects into
  the agent's system prompt on every chat in the project. This is how work carried out in
  one chat stays "in context" for later chats. You can ask the agent to **update the
  project's memory** so that what's been done is remembered, and you can also **edit the
  memory manually** in this panel and click **Save memory**.

Keeping a project active and its memory current is the key to long-running research that
spans many sessions.

---

## The Previous Chats panel

Past conversations are saved automatically. The **Previous Chats** panel lets you return
to them.

![Previous Chats panel — filter by project, then rename, export, or delete chats](images/05-previous-chats.png)

- **Filter by project** to see only that project's chats (or tick *Show all projects*).
- **Open** a chat to load its full history and continue where you left off — the agent
  resumes with the complete context.
- For each chat you can **Rename** it, **Export** it (to save or share the conversation),
  or **Delete** it.

---

## The Viewer

If you work with medical-imaging data, ReCo includes an integrated **Viewer** for CT/MR
volumes and their organ/tissue contours (segmentation masks).

![Viewer — multiplanar CT with overlaid segmentation masks and a 3D rendering](images/06-viewer.png)

You can use the Viewer in two ways:

- **Manually:** click **Open image** to load a scan and **Open mask(s)** to overlay
  contours. Adjust per-layer opacity and colormaps, scroll through the axial / coronal /
  sagittal planes, inspect the 3D rendering, and use the brush/eraser tools to **curate**
  masks. You can then **Save** or **Export** the edited labelmap.
- **Driven by the agent:** after the agent produces a segmentation, you can simply ask it
  to *open the CT and the contours in the viewer* for inspection — a natural workflow for
  reviewing and curating results the agent generated for you.

---

## Disclaimer

**Important safety & data-handling notice**

ReCo is an autonomous agent that can read and write files, run code, and call external
model APIs on your behalf. Please read the following before using it.

- **Research use only — not for clinical use.** ReCo is intended for research purposes.
  It is **not** certified as a medical device and must **not** be used for clinical
  diagnosis, treatment planning, or patient management.
- **Unsupervised actions can be destructive.** If ReCo is not run in a safe, isolated
  environment, and if you do **not** review and approve each action (set the permission
  policy to *ask for approval* rather than allow-all), the agent may access sensitive
  data, read or modify patient data, and **delete files and folders** on this machine.
- **Risk of patient-data leakage via the API.** Unless the core reasoning engine is a
  **local LLM**, reading DICOM metadata or CSV/Excel files that contain patient-sensitive
  information sends that content to a third-party model provider through the API call —
  which may expose protected health information outside your control. Use a local model,
  and/or fully de-identified data, when handling sensitive records.
- **No warranty.** The developers make no guarantees regarding the accuracy or
  suitability of any models or analyses produced with this software. Use is entirely at
  your own risk. The software is provided "as is", without warranty of any kind, express
  or implied.

You are responsible for complying with the data-protection, privacy, and ethics
obligations that apply to your data and jurisdiction.

---

## Contact

**Developer:** Dr. Eleftherios Tzanis
**Email:** tzaniseleftherios@gmail.com

---

## How to cite ReCo

If ReCo contributes to your research, please cite the ReCo preprint and the bundled
tools original papers:

- https://doi.org/10.64898/2026.07.14.26358025
- https://doi.org/10.1148/ryai.250923
- https://doi.org/10.1016/j.ejrai.2025.100044

### Bundled frameworks & references

ReCo's pre-installed MCP servers wrap the open-source frameworks below. When you use these
tools, please also cite the relevant upstream work:

- **nnU-Net** (nnU-Net Segmentation Toolkit; also underlies TotalSegmentator) — Isensee F,
  Jaeger PF, Kohl SAA, Petersen J, Maier-Hein KH. *nnU-Net: a self-configuring method for
  deep learning-based biomedical image segmentation.* Nature Methods. 2021;18(2):203–211.
  https://doi.org/10.1038/s41592-020-01008-z
- **TotalSegmentator** — Wasserthal J, Breit H-C, Meyer MT, et al. *TotalSegmentator:
  Robust Segmentation of 104 Anatomic Structures in CT Images.* Radiology: Artificial
  Intelligence. 2023;5(5):e230024. https://doi.org/10.1148/ryai.230024
- **PyRadiomics** — van Griethuysen JJM, Fedorov A, Parmar C, et al. *Computational
  Radiomics System to Decode the Radiographic Phenotype.* Cancer Research.
  2017;77(21):e104–e107. https://doi.org/10.1158/0008-5472.CAN-17-0339
- **MONAI** (Medical Image Classification 3D) — Cardoso MJ, Li W, Brown R, et al. *MONAI:
  An open-source framework for deep learning in healthcare.* arXiv:2211.02701. 2022.
  https://doi.org/10.48550/arXiv.2211.02701
- **PyTorch** (Medical Image Classification 2D & 3D) — Paszke A, Gross S, Massa F, et al.
  *PyTorch: An Imperative Style, High-Performance Deep Learning Library.* NeurIPS.
  2019:8024–8035. https://doi.org/10.48550/arXiv.1912.01703
- **scikit-learn** (Feature Importance & Selection, performance metrics) — Pedregosa F,
  Varoquaux G, Gramfort A, et al. *Scikit-learn: Machine Learning in Python.* JMLR.
  2011;12:2825–2830. https://jmlr.org/papers/v12/pedregosa11a.html
- **PyCaret** (PyCaret Classification & Regression) — Ali M. *PyCaret: An open-source,
  low-code machine learning library in Python.* 2020. https://pycaret.org
- **SciPy / seaborn** (EDA Analysis) — Virtanen P, Gommers R, Oliphant TE, et al. *SciPy
  1.0: fundamental algorithms for scientific computing in Python.* Nature Methods.
  2020;17:261–272. https://doi.org/10.1038/s41592-019-0686-2
