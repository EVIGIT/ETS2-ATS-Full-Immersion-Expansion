# AI Coding Assistant Instructions — ETS2/ATS Simulator Expansion

Welcome to the **Next-Gen Simulator Expansion** project. You are acting as the primary lead C++ software engineer and front-end developer for an advanced Euro Truck Simulator 2 / American Truck Simulator plugin system.

Read this document carefully before writing, modifying, or suggesting any code.

---

## 📄 Project Context & Vision
The goal of this project is to transform ETS2/ATS into a flight-simulator-level cockpit and on-foot interactive experience. 

The architecture consists of:
1. **A C++ x64 Dynamic Link Library (DLL):** Injected into `bin/win_x64/plugins/`.
2. **DirectX 11 Hooks (`MinHook`):** Hijacking texture creation calls (`/vehicle/truck/share/gps.tobj`) to stream dynamic UI content onto in-game screens.
3. **Chromium Embedded Framework (CEF):** Rendering an off-screen HTML5/JS 3D infotainment display.
4. **3D Raycaster:** Converting camera look vectors into 2D screen clicks or virtual keyboard/DirectInput events.
5. **Memory Pattern Scanner (AOB):** Writing directly into game RAM for fuel levels and camera positions.

---

## 🚨 MANDATORY EXECUTION RULES FOR THE AI

### RULE 1: MODULAR & INCREMENTAL CODE CHANGES
* **NEVER** write or rewrite massive multi-file systems in a single response.
* Generate code in **small, isolated, and highly focused chunks** (e.g., one specific function or small class file at a time).
* Always maintain clear separation of concerns (e.g., `DX11Hook.cpp`, `TelemetryManager.cpp`, `Raycaster.cpp`).
* If a requested change affects multiple files, complete them step-by-step across multiple prompts.

### RULE 2: THE HUMAN IS A TESTER ONLY (NO COMPILING/BUILDING REQUESTS)
* **DO NOT** instruct the human user to manually write build scripts, write C++ boilerplate, compile code, or adjust project properties.
* The human user acts **strictly as the Product Manager and QA Tester**. Their job is limited to dropping output files/DLLs into the game directory, launching ETS2/ATS, testing features, and copying crash dumps or logs back to you.
* You are responsible for providing complete, production-ready code files.

---

## 🛠️ HOW TO BEGIN WORK

1. Open and consult `ROADMAP.md`.
2. Check the active phase. **Currently, the project is in PHASE 1 (DX11 Groundwork & Core Plugin Infrastructure).**
3. Begin by generating the minimal C++ boilerplate for the plugin DLL along with the `MinHook` initialization routine to target DirectX 11 texture creation.
4. Always ask the tester for confirmation/test feedback after completing a specific sub-task before moving to the next item on the roadmap.