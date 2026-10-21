# ETS2 / ATS Next-Gen Simulator Expansion — Project Roadmap

This document outlines the phased development plan for an immersive, flight-simulator-level cockpit and on-foot interaction plugin for Euro Truck Simulator 2 (ETS2) and American Truck Simulator (ATS).

---

## 🎯 Architectural Overview
* **Host Engine:** Prism3D (ETS2 / ATS x64)
* **Graphics Hooking:** DirectX 11 (`MinHook` targeting `ID3D11Device::CreateTexture2D` / dynamic texture swapping)
* **UI Engine:** Chromium Embedded Framework (CEF) / CefSharp Off-Screen Rendering (OSR)
* **Interaction:** 3D Camera Raycasting $\rightarrow$ 2D UV coordinate mapping / DirectInput keypress simulation
* **Memory Management:** Pattern scanning (AOB) for runtime memory manipulation

---

## 📍 Phase 1: DX11 Groundwork & Core Plugin Infrastructure (Current Phase)
> **Goal:** Establish a stable C++ x64 DLL plugin framework, hook DirectX 11 rendering, and replace the in-game GPS screen texture with a dynamic writable buffer.

- [ ] **1.1 Plugin Boilerplate:** Build the C++ DLL base supporting ETS2/ATS plugin initialization via SCS Telemetry SDK entry points.
- [ ] **1.2 DirectX 11 Hooking:** Integrate `MinHook` to target DirectX 11 swap chains and texture creation calls.
- [ ] **1.3 GPS Texture Isolation:** Filter engine texture load calls to target `/vehicle/truck/share/gps.tobj` or equivalent material shaders without modifying stock instrument clusters.
- [ ] **1.4 Texture Buffer Swapping:** Allocate a dynamic `ID3D11Texture2D` buffer and hijack the GPS screen target to display solid color / test pattern frames cleanly at native framerates.
- [ ] **1.5 Telemetry SDK Link:** Initialize SCS Telemetry SDK hooks to pull core vehicle telemetry (speed, RPM, fuel, location, orientation).

---

## 📍 Phase 2: Chromium UI Integration & Interactive Infotainment
> **Goal:** Embed an off-screen Chromium instance (CEF) and stream an HTML5/JS 3D infotainment interface directly onto the hijacked display.

- [ ] **2.1 CEF Off-Screen Renderer (OSR):** Embed CEF inside the plugin to render HTML/JS pages to a shared pixel memory buffer.
- [ ] **2.2 DX11 Pixel Buffer Stream:** Copy CEF frame buffers directly into the hijacked DirectX 11 GPS texture per frame.
- [ ] **2.3 Front-End Web Stack:** Build the HTML/JS central console app featuring:
  - 3D Navigation Map
  - Media/Audio Player Interface
  - Vehicle Telemetry & Fleet Diagnostics Hub
- [ ] **2.4 3D Raycasting Engine:** Implement camera-to-mesh raycasting converting 3D click vectors on the central console into 2D UV screen inputs sent directly into CEF.

---

## 📍 Phase 3: Physical Cockpit Controls & Ignition Mechanics
> **Goal:** Extend raycasting to physical interior controls to allow direct in-cab interaction with switches, stalks, and the ignition.

- [ ] **3.1 Dynamic Mesh Interaction Matrix:** Build a JSON-driven look-up table mapping 3D cabin interior mesh names to Virtual Key Codes / DirectInput actions.
- [ ] **3.2 Ignition Key System:**
  - First click: Turn electrics/accessory power ON (Boots up CEF Infotainment screen).
  - Hold/Double click: Crank starter motor and execute `KEY_ENGINE`.
- [ ] **3.3 Stalk Controls:** Map indicator stalk (up/down clicks) and wiper stalk (click to step speed settings).
- [ ] **3.4 Steering Wheel & Dash Switches:** Map wheel buttons (Cruise Control, Trip Computer) and dashboard toggles (Hazard lights, Differential Lock, Retarder).

---

## 📍 Phase 4: On-Foot Exploration & Door Animations
> **Goal:** Decouple the interior camera to allow full first-person walking and seamless entry/exit mechanics using existing engine animation channels.

- [ ] **4.1 Option A "Window Hook" Door Animation:** Remap power window animation tracks to swing the physical driver-side door open $65^\circ$ upon exit/entry.
- [ ] **4.2 Audio State Engine:** Trigger mechanical latch audio clips and un-muffle ambient/engine exterior sounds when doors swing open.
- [ ] **4.3 Camera Matrix Decoupling:** Detach camera matrix from the interior driver head node into a First-Person WASD + Mouse Look camera engine.
- [ ] **4.4 Ground & Collision Snapping:** Apply downward terrain raycasting to keep the player attached to physical road/ground heights.

---

## 📍 Phase 5: Manual Refueling & Station Interactions
> **Goal:** Enable hands-on refueling at petrol stations using on-foot interactions and live RAM memory writing.

- [ ] **5.1 Safety State Machine:** Require engine shutdown (`engine_running == false`) and zero velocity before unhooking fuel nozzles.
- [ ] **5.2 On-Foot Nozzle Attachment:** Raycast pump handles to attach nozzle geometry to the player's view model.
- [ ] **5.3 Method 1 Memory Injection:** AOB pattern scan game RAM to locate the runtime fuel memory offset and write updated float values directly into game memory while holding `[E]` at the fuel cap.
- [ ] **5.4 Live CEF Gas Station Meter:** Render live Liters / Cost overlay on screen while refueling, automatically triggering financial deductions upon returning the hose.

---

## 📍 Phase 6: Tire Wear Diagnostics & Garage Repairs
> **Goal:** Implement mechanical wear penalties, hydroplaning risks, and garage-based servicing.

- [ ] **6.1 Axle Wear Tracking:** Read individual wheel wear floats (`truck.wear.wheels[i]`) via SCS Telemetry.
- [ ] **6.2 Dynamic Handling Penalties:** Inject traction loss and increased braking distances when wear passes $75\%$ (with hydroplaning penalties in rain).
- [ ] **6.3 Option B Garage Servicing:** When parked in service bays, expose an itemized repair interface on the CEF Fleet Diagnostics touchscreen to authorize axle tire replacements and reset wear memory floats to `0.0`.

---

## 📍 Phase 7: Sleeper Berth Transitions & Interactive Rest Loop
> **Goal:** Replace static sleep blackouts with smooth camera transitions into the truck's sleeper compartment.

- [ ] **7.1 Smooth Camera Lerp (`SleeperCamera.cpp`):** Interpolate camera coordinates $1.2\text{ m}$ backward into the rear sleeper berth when resting at designated stops.
- [ ] **7.2 Curtain Animation Hook:** Trigger interior curtain animations (`curtain_anim`) to pull privacy curtains across the windscreen during rest cycles.
- [ ] **7.3 Interactive CEF Alarm Clock:** Render an alarm clock interface on the bed view to select rest durations (6h / 8h / 10h), advancing game time upon confirmation and playing a wake-up chime before panning back to the driver seat.