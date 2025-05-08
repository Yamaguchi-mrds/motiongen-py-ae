# Motion Graphics Production by Structural Definition: Integration of After Effects and Python
> 🗓️ Created: May 8, 2025  
> 🖋️ Author: Yosuke Yamaguchi
# 1. Introduction
## 🔍 Terminology and Basic Concepts

Below are simplified definitions of key technical terms frequently used in this document. More detailed technical definitions will be supplemented in each chapter where appropriate.

- **Structure-driven**: A method of generating visuals based on data structures defined in code, rather than GUI operations.
- **Bezier Path**: Curve data with control points used to construct smooth shapes; a fundamental format in After Effects Shape Layers.
- **JSON (JavaScript Object Notation)**: A lightweight text format used to describe data structures. Used as an intermediary file format between Python and AE.
- **JSX (ExtendScript)**: A JavaScript-based scripting language used for automation in After Effects.
- **Morphing**: An animation technique that transforms one shape into another. In this project, it's controlled via Bezier structures.
- **GUI (Graphical User Interface)**: A visual interface format that users operate with mouse etc. AE adopts this format.

---

This document proposes a motion graphics production environment that replaces GUI-driven production with a "structure-driven" approach by linking two fundamentally different tools: Adobe After Effects (hereinafter AE) and Python.

While AE provides an excellent editing environment for sophisticated visual expressions, it has fundamental limitations when it comes to behavior design that incorporates control theory or mathematical approaches. Although GUI-based intuitive operations are powerful, they show clear limitations in applications that require controlling complex structures repeatedly or synchronizing with external data.

Mechanisms within AE, such as Expressions and ExtendScript, have been explored, but none of them are suitable for modern structural design or abstract control. They also pose significant constraints for flexible expansion and reuse.

From these challenges, the idea emerged to clearly divide roles: “structure definition and control handled by Python, and rendering handled by AE.”

Python excels in numerical processing, geometric operations, external application integration, and data visualization. In this project, all shape structures are defined and constructed in Python as Bezier paths. AE receives this structural information via JSON and handles only the rendering.

This approach makes it possible to construct theoretically driven shape generation, deformation, and structure-based morphing animations—tasks difficult to realize through GUI operations. Furthermore, abstraction and functionalization of structures and reuse of configurations become possible. This establishes a new collaborative model: “AE as a rendering device” and “Python as a structure design tool.”

---

### The following table compares the traditional AE environment with the proposed structure-driven approach:

| Perspective         | Traditional (AE-centric)            | Proposed (AE + Python Integration)      |
|---------------------|--------------------------------------|------------------------------------------|
| Production Method   | GUI-centric operation               | Structure generation and control by code |
| Language Environment| ExtendScript (equivalent to Java 1.5)| Python 3 (modern syntax and rich libraries) |
| Structural Abstraction | Difficult (manual path editing)    | Defined and reusable via functions/data structures |
| External Integration| Limited (self-contained within AE)  | Real-time integration with other apps/external signals |
| Motion Control      | Timeline editing or simple expressions | Driven by geometric operations and mathematical models |

The next chapter will describe the background leading to this concept and the issues encountered in the conventional environment.


# 2. Background and Awareness of Issues

Traditionally, structural control in video production has relied on methods that are completed entirely within AE (Adobe After Effects). AE excels at visual operations via GUI and has a strong reputation in motion graphics production.  
However, when structural designs based on control theory or complex algorithm-driven behaviors are needed, the following clear limitations arise:

- **Human-dependence of operations**: In a GUI-based system, replicating the same structure requires repeated manual actions, which lacks reusability and automation.
- **Limited expressiveness**: AE Expressions allow for simple mathematical controls but are unsuitable for building complex logic.
- **Outdated development environment**: AE scripts based on ExtendScript (equivalent to JavaScript 1.5) do not support modern programming paradigms or debugging features.

For example, automatic shape placement, dynamic Bezier deformation, or generation control based on multiple conditions are difficult to achieve with AE alone. Expression's hierarchical structure is limited, making dynamic structural switching and advanced recursive logic difficult to implement.

---

## 🧩 Transition: From JavaScript to Python

Initially, development attempted to utilize AE's standard features, implementing with Expressions and ExtendScript. Since Bezier paths and transformations could be described within AE, some results were achieved in the prototype phase.

However, fundamental barriers arose:

- The language is outdated, lacking strong **object orientation** and **function reusability**
- **Insufficient traceability and log output** during errors
- Weak **code completion and syntax support** in the JSX editor

As a result, the policy shifted toward migrating the structural design process to an external environment. To handle control design and structural generation more flexibly, Python was adopted.

---

## 🚀 Significance of Introducing Python

Python was chosen for the following reasons:

- **Strong computational capabilities**: Efficient description of vector/matrix operations, signal analysis, interpolation, etc.
- **General-purpose data handling**: Native support for formats like JSON, CSV, images, and audio
- **Rich library ecosystem**: NumPy, OpenCV, SciPy, Pillow, TensorFlow, etc.
- **Integration with other apps**: Designed with Python-based control in tools like Blender and TouchDesigner

Moreover, defining shapes as structured Bezier data (vertices / inTangents / outTangents) enables **functional control of the entire structure**, including dynamic path generation and morphing.

---

## 🔁 From GUI to Structure: A Paradigm Shift

The core concept here is a shift from “manual GUI operation” to “design via structural definition.”

GUI-style workflow:

1. Draw a shape using the Pen tool  
2. Add motion in the timeline  
3. Control animation with keyframes

In contrast, the structural approach:

1. Generate point groups using Python functions  
2. Convert them into Bezier structures and build morph paths  
3. Serialize as JSON and send to AE for rendering

This conceptual shift enables video production based on **reusable structural design**, allowing for complex transformations, integrations, and reconstructions.

---

## Beyond Tools: A New Production Philosophy

By defining shapes as Bezier structures (vertices / inTangents / outTangents), functional control of the entire structure—including dynamic path generation and morphing—becomes possible.

Furthermore, most existing integrations between Python and After Effects focus on productivity tools such as "asset management" or "batch layer generation." In contrast, this project offers a fundamentally different approach—**designing the video itself as a structure**. By clearly separating structural definition, control, and rendering into reusable data structures, it presents a shift not just in production tools, but in production philosophy itself.

---

The next chapter introduces the overall architecture of the four-phase control system designed as a solution to these issues.


# 3. System Overview

The proposed “Structure-based Video Generation Environment with AE + Python” clearly separates the roles of structure definition, logic processing, and rendering control, assigning each role to the most appropriate technology stack.

This system is composed of four clearly defined phases:

[1] Generation (GEN)  
　↓ Python: Generate shape point groups (functional structure)

[2] Structuring (BUILD)  
　↓ Python: Convert Bezier → AE layer data structure

[3] Data Export (EXPORT)  
　↓ Python: Save as JSON (intermediate file)

[4] Rendering Execution (DRAW)  
　↓ After Effects: Read JSON with JSX → Draw layers

---

## 📐 Overview of Each Phase

### ① GEN: Structure Generation

- Arbitrary shapes (circles, stars, polygons, image-based forms, etc.) are generated functionally in Python.
- The generated shapes are described as Bezier structures (`vertices`, `inTangents`, `outTangents`).
- To support morphing, consistency in vertex count and order is ensured.

### ② BUILD: Structuring for AE

- Bezier structures are converted into AE-readable layer-formatted dictionaries.
- The data includes layer names, fill settings, positions, styles, etc.
- Unified processing via functions like `build_shape_layer()`.

### ③ EXPORT: Output as Intermediate Format

- The finalized structural data is saved as a JSON file.
- This JSON functions as the "blueprint" for drawing instructions to AE.
- Exported with a clear file structure (e.g., `/s04_export_json/ae_data.json`).

### ④ DRAW: Visual Rendering in AE

- A JSX script (`load_json_ae.jsx`) reads the JSON and generates layers in AE.
- Bezier structures are restored as `ShapeLayer`s and visualized.
- Drawing is handled using AE’s built-in scripting mechanisms (auto-execution).

This structural separation enables flexible design of dynamic shape generation and external data integration—previously difficult within AE’s timeline editing. With AE acting as a visualization tool and Python as a structure design engine, reusability and extensibility are greatly enhanced.

This approach minimizes dependency on AE while significantly expanding the freedom of structural design and creative expression. For example, tasks like random placement, dynamic point control, or complex morphing—challenging to perform manually in AE—can now be flexibly defined in Python code.

---

### 📋 Supplement: Phase Responsibility Summary

| Phase  | Responsible     | Main Task                                      |
|--------|-----------------|------------------------------------------------|
| GEN    | Python          | Shape design and generation (Bezier structure) |
| BUILD  | Python          | Structuring for AE (with style and position)   |
| EXPORT | Python          | Intermediate output as JSON (shared format for AE) |
| DRAW   | After Effects   | JSON reading and layer rendering (final visual stage) |

---

## 🧠 A Shift from "Editing" to "Structural Drawing"

This approach flips the typical paradigm: rather than "editing inside AE to produce visuals," it focuses on "designing the structure in Python and letting AE render it."

For example, the following workflows become possible:

> A mathematically defined dodecagon → Converted to a styled, positioned structure → Exported as JSON → Rendered in AE  
> Audio data converted into a spectrum → Point group generated → Animated structure built → Displayed dynamically in AE  

By clearly separating structure and expression, freedom of control, integration flexibility, and reusability have all been dramatically improved.

---

The next chapter delves into the design policy and modular structure of each phase comprising the proposed system.


# 4. Design Principles

The design of this system is based on three pillars: “structure for expression,” “controllable data design,” and “expandable system modularity.”

Whereas traditional video production is a process that operates toward a final output (the video), this concept takes a **reversed approach**: design the structure (data) and hand it off to a rendering engine.

---

## 🧱 Basic Unit of Structure Definition: Bezier Paths

All shape data is centrally managed using a **Bezier curve structure** compatible with Adobe After Effects:

- `vertices`: List of vertex coordinates
- `inTangents`: Control points leading into each vertex
- `outTangents`: Control points leading out of each vertex
- `closed`: Optional flag indicating whether the path is closed

Unifying the data structure in this way provides the following benefits:

- Diverse shapes (circles, polygons, stars, free-form curves) managed under one system
- Morphing becomes possible if vertex count and order are fixed
- Easy conversion to JSON → Simplified AE integration

---

## 🧩 Modular Composition and Separation of Responsibilities

On the Python side, code is **modularized by function** with clearly defined responsibilities:

| Module              | Role                                                   |
|---------------------|--------------------------------------------------------|
| `shape_gen.py`      | Generates point groups in Bezier format (circle, polygon, star, etc.) |
| `build_layer.py`    | Converts Bezier data into AE layer dictionary structure |
| `write_ae_json.py`  | Outputs AE data as JSON                                |
| `load_json_ae.jsx`  | Loads JSON in AE and renders as layers                 |
| `run_aefx_json.py`  | Launches AfterFX via CLI and runs rendering script     |

This separation significantly improves **debuggability, reusability, and extensibility**, providing a flexible foundation that can scale to advanced use cases.

---

## 📄 Benefits of JSON as an Intermediate Format

Integration with AE is achieved by outputting the structured data as a JSON file.  
Adopting this intermediate format yields several design advantages:

- **AE can be treated as a black box**: Complete separation between design and rendering
- **Human-readable structure**: Ideal for debugging, formatting, and logging
- **Cross-app compatibility**: JSON can be handled by other tools like TouchDesigner or Unity

Here is an example of actual AE JSON output:

```json
{
  "name": "Circle_A",
  "type": "bezier",
  "path": {
    "vertices": [[0, -100], [100, 0], [0, 100], [-100, 0]],
    "inTangents": [[30, 0], [0, -30], [-30, 0], [0, 30]],
    "outTangents": [[-30, 0], [0, 30], [30, 0], [0, -30]]
  },
  "style": {
    "fill": true,
    "color": [1, 0.2, 0.2],
    "opacity": 100
  },
  "transform": {
    "position": [960, 540]
  }
}
```

As shown, structure and visual data are clearly separated, making this approach adaptable to non-AE environments as well.

---

## 🧠 Data-Driven Reusability

By shifting from “GUI-based creation” to “structured design,” each dataset becomes reusable and reconstructable via logic.

For example:
- Automatically generate multiple sizes by scaling the same point group
- Parameterize color, opacity, and position for random placement
- Reconstruct and re-output designs via GUI or external signals

This design is adaptable for various applications, including auto-generation, structural motion graphics, academic simulations, educational content, and visual effect templates.

---

The next chapter will demonstrate practical expressions based on this architecture and its potential for integration with other tools.


# 5. Applications and System Integration

This chapter provides a comprehensive overview of the technical foundations and potential applications of the proposed system.  
From implemented animation techniques to GUI/AI extensions and integration with other applications, it illustrates the flexibility and future prospects of structure-driven video production.

---

## 5.1 Practical Applications Based on Implementation

### 🌀 Bezier Morphing Animation
- Interpolate between shapes defined with the same vertex count and order using `lerp_points()`
- On the AE side, use `path.setValueAtTime()` to draw smooth time-based transitions
- Enables theoretically-driven dynamic deformation based on structure

### 🔊 Signal Analysis and Audio-Responsive Expression
- Extract audio spectra using tools like NumPy, SciPy, and librosa, and map them to structural changes
- Utilize sensor inputs (MIDI, accelerometers, environmental sound) for structure control
- Reflect these changes in AE via Python to synchronize sound and visuals

### 🖼️ Image-Driven Structure Generation
- Use OpenCV to build a pipeline: contour extraction → point cloud → Bezier conversion
- Map brightness and hue to structure deformation or fill style to generate image-driven graphics

---

## 5.2 Advanced Concepts and Future Expansion

### 🎛️ GUI-Assisted Structural Design
- Build interactive structural design tools using Tkinter or PyQt
- Visually manipulate parameters like color, size, shape, and layout, and convert directly to AE-compatible JSON

### 🧠 Advanced Analytics and AI Integration
- Apply frequency analysis, statistical modeling, or clustering to automatically classify or morph structures
- With TensorFlow or PyTorch, design structures using generative models or predict structure behavior
- Use Mediapipe to enable gesture-based structure control from video input

---

## 5.3 Integration Possibilities with Other Tools

Because the system defines structures in JSON and handles control in Python, it can integrate with various applications. The following are categorized use cases:

### 🎥 Video and Animation Tools

| Tool             | Integration Details |
|------------------|---------------------|
| After Effects     | Structure-based rendering via JSX + JSON (main output platform) |
| Blender           | 3D structure generation and animation control via Python API; supports bidirectional integration |
| TouchDesigner     | Real-time structure injection and visual sync using Python + OSC |
| Nuke (Foundry)    | Inject structural data via Python in the node graph; can be used alongside AE |

### 🎧 Audio, Sensor, and Signal Tools

| Tool                        | Integration Details |
|-----------------------------|---------------------|
| Ableton Live + Max for Live | Feedback from audio signal analysis into structure parameters |
| Pure Data / MaxMSP          | Real-time structure control using OSC and sensor input |
| Open Sound Control (OSC)    | Bidirectional communication allows arbitrary signal-to-structure conversion |

### 🛠 CAD, Design, and Modeling Tools

| Tool                        | Integration Details |
|-----------------------------|---------------------|
| Fusion 360                  | Script-based structural design convertible into AE paths (with limitations) |
| FreeCAD                     | Structural designs in Python applicable to AE via exported data |
| Rhino / Grasshopper         | Functions as a bridge to convert geometric data into motion graphics |

### 🧠 AI and Analytics Libraries

| Library                     | Integration Details |
|-----------------------------|---------------------|
| OpenCV / Mediapipe          | Apply gesture detection and video analysis to structure generation/control |
| TensorFlow / PyTorch        | Enable auto-design and motion prediction via generative models or classification |
| scikit-learn / statsmodels  | Use statistical methods to abstract and analyze structural composition patterns |

---

## 5.4 Technical Core and Design Philosophy

The reason for such diverse integration and application possibilities lies in the adoption of the following design principles:

- **Structure described in JSON**: A standardized, human-readable data format compatible with non-AE tools
- **Control and computation written in Python**: Covers structure generation, signal processing, external communication, and GUI
- **AE used solely as a rendering device** while design/control is independently managed externally

This ensures flexibility to evolve into a structure-driven, control-oriented video production environment with minimal reliance on After Effects.

---

The final chapter summarizes the outcomes of this research and discusses future directions for system expansion and implementation.



# 6. Conclusion & Future Works

This paper has proposed, implemented, and validated a system concept that enables structure-defined motion graphics generation, which was previously difficult to achieve in GUI-centric video production environments, by integrating After Effects (AE) and Python.

The core idea—designing shape structures logically in Python and passing them to AE via JSON as an intermediate format—dramatically enhances controllability, reusability, and extensibility in video production. The ability to generate shapes, deform structures, and control behaviors algorithmically, without relying on manual GUI operations, is a central achievement of this study.

---

## ✨ Key Achievements

- Clear division of roles between AE and Python (AE = rendering engine, Python = structure engine)
- Unified structure management via Bezier format, enabling morphing and multi-shape generation
- Use of JSON as an intermediate format ensures interoperability with other applications
- Modular design enables easy extension and reuse of functionalities
- Established pathways for integrating structural control with external data such as signals, images, audio, AI, and sensors

---

## 🚧 Future Directions

Looking ahead, this concept can be further developed along the following lines:

### 1. GUI Layer Implementation
- Develop GUI tools that allow non-programmers to design shape structures in Python
- Enable intuitive layout, shape transformation, and parameter adjustments for color and animation

### 2. Enhanced Real-Time Integration
- Use OSC or WebSocket to enable **real-time structure updates and reflection in AE**
- Create bidirectional systems that interact with sensors, audio, and interactive devices

### 3. AI Integration
- Link with generative AI (e.g., structure prediction, shape suggestions, automatic behavior generation)
- Use analytical AI for pattern classification and expression optimization

### 4. Use in Education and Research
- Apply the system in educational settings for mathematical modeling, structural engineering, and visual programming
- Build an integrated creative learning environment combining expression, design, and programming

### 5. Diversification of Output Formats
- Expand output beyond AE to 3D, node-based, or web-based environments (Blender, Unity, WebGL, etc.)
- Support multi-format structure-to-visual transformations such as PDF, SVG, and video files

---

## 💬 Final Thoughts

This study presents a new approach to video production based on the philosophy that “expression is born from structure” and “structure is definable.” The perspective of structure-centered video production has broad versatility and scalability, spanning control, algorithms, analysis, and creative direction.

By bringing together AE as a rendering platform and Python as a structure design environment, this system represents a departure from traditional constraints and serves as the starting point for a new “structure-driven creative environment.”

It is hoped that continued refinement and application of this concept will contribute to the evolution of creative expression technologies.

Details about the production setup and the author's technical background are provided in Appendices A and B.



## A. Development Structure and Production Process

This concept was independently designed and implemented by the author.  
Below is a concise record of the development background and environment setup process.

### Developer Skills
- Basic knowledge of general-purpose languages such as C and VB
- Experience building component manufacturing automation systems using LabVIEW
- Implementation-oriented thinking based on engineering knowledge in control and electrical systems

### Development Timeline (2025)
- Apr 27: Began testing initial control using AE ExtendScript (JSX)
- May 05: Started implementing the Python-based structure generation engine (alongside learning Python)
- May 08: Completed core configuration of the AE + Python integrated Bezier rendering system

### Use of AI Assistance
During development, ChatGPT was actively used for validating structural designs, script syntax, and formatting algorithms.  
This significantly accelerated trial-and-error processes and ensured design consistency in the construction of a non-GUI expression environment.



## B. Author Biography and Technical Achievements

**Author Name:** Yosuke Yamaguchi

The author previously worked at Yaskawa Electric Corporation, in the R&D Department for Encoder Element Technologies.  
He has over 15 years of experience as an engineer. During his tenure, he achieved the following:

- Technical Patents: 3 registered (e.g., Japanese Patent Application 2011-064608)
- Invention Award: Received the "Fukuoka Governor's Award" in 2017 from the Japan Institute of Invention and Innovation  
  - Details: https://koueki.jiii.or.jp/hyosho/chihatsu/H29/jusho_kyushu/index.html

Afterward, he became an independent video creator, engaging in expression research and development that draws on his engineering background.

### Areas of Expertise and Summary of Achievements

- **Fields of Expertise**: Electrical / Mechanical / Wave Optics / Geometrical Optics / Control Engineering / Signal Processing / Programming Languages (C, C++, VB, LabVIEW)

- **Major Work Experience**:
  - Design and development of high-efficiency factory equipment
  - Design of high-precision optical encoder calibrators
  - Development of correction software utilizing frequency analysis
  - Optical component technologies for optical discs (CD/DVD/Blu-ray)
  - Design and operation of hardware environmental testing and evaluation

---

### 📅 Recent Skill Acquisition History (2024–2025)

Learning of the software and scripting languages related to this concept was carried out concurrently with the planning and implementation of the system. Below is a summary of major learning milestones:

- **Dec 15, 2024**: Began learning basic editing techniques in After Effects
- **Apr 17, 2025**: Started learning ExtendScript (JSX) to explore AE scripting
- **Apr 27, 2025**: Created the first custom JSX script (started from zero knowledge of JavaScript/Java)
- **May 4, 2025**: Began using Python for the first time, parallel to the system implementation
- **As of May 8, 2025**: Despite only four days of Python usage, successfully prototyped a motion graphics system based on structural logic

This research represents a mid-career skill transformation, aiming to fuse existing engineering experience with emerging creative technologies.

Created on 2025.05.08
