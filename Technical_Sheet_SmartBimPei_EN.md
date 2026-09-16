# SmartBimPeï - Technical Sheet & Architecture

**SmartBimPeï** (formerly SmartClashDetector) is a professional, custom-developed plugin for Autodesk Revit®. Initially designed to revolutionize geometric synthesis (Clash Detection), the plugin has evolved into a comprehensive suite of 5 distinct modules covering the entire spectrum of BIM Management: Geometric Synthesis, Data Quality Audit, Model Cleaning, Executive Dashboard, and Quantity Takeoff (QTO).

> [!TIP]
> **Technical Objective:** Provide "surgical" precision on complex geometries (curved MEP networks, slabs), guarantee absolute fluidity in Revit when processing thousands of data points, and ensure foolproof compliance control (FM / Handover).

---

## 1. Software Architecture & Performance

The creation of SmartBimPeï required overcoming numerous challenges specific to the Revit API:

*   **Asynchronous Architecture (Thread-Safe)**: Utilization of separate threads (Asynchronous Tasks) for geometric calculations and parameter verification. Communication back to the WPF interface is handled via `ExternalEvent` (`IExternalEventHandler`), preventing any "freezing" of Revit, even when analyzing thousands of clashes or properties.
*   **Stability & Safety (Anti-Crash)**: Implementation of `TransactionGroup` and `RollBack` during visualizations. Actions that virtually modify the model (to isolate an object or apply transparency) are cleanly managed or rolled back at the end of the script. The model is never modified accidentally.
*   **Professional Codebase (MVVM)**: Implementation of the MVVM (Model-View-ViewModel) pattern and strict separation of `Views` (`.xaml` files), `Models` (Data), and `Handlers` (Revit API business logic).

---

## 2. Module 1: Smart Clash Detector (Geometric Synthesis)

The historical core of the plugin. It advantageously replaces Revit's native interference check by providing advanced filtering options and automated creation of openings.

### Technical Features:
*   **Multi-Criteria Analysis**: Cross-checking between Selection A and B (Ducts, Pipes, Cable Trays, Walls, Slabs...).
*   **Advanced Geometric Calculation (BoundingBoxes)**: Precise determination of complex `BoundingBoxes` (including Linked Models with their coordinate `Transform`) for perfect conflict targeting in 3D space.
*   **Geometric Tolerance & Sliver Tolerance**: Filtering of micro-conflicts (e.g., insulation contact or standard site tolerances) according to a customizable millimeter threshold.
*   **Intelligent Auto-Opening**: Automated insertion of "void" families (Rectangular or Circular voids) at the exact intersection location, managing a customizable "Opening Margin" (e.g., +50mm) to anticipate insulation.
*   **"Surgical" Visual Rendering**: Automatic transition of the 3D view with a transparent grey template for context, and opaque high-contrast colors for the two clashing elements.

---

## 3. Module 2: BIM Health Dashboard

A decision-making tool for company executives and project directors.

### Technical Features:
*   **Global Health Score (KPI)**: An algorithm that reads the results from all other modules (unresolved clashes, MEP duplicates, non-compliant data) to calculate a score out of 100 representing the BIM model's health.
*   **Financial Indicators**: Cross-referencing detections (duplicates, quantities) with an estimated cost database to price the financial impact of modeling errors.
*   **Executive Report**: Generation of structured exports (PDF, Excel) summarizing KPIs for management reviews.

---

## 4. Module 3: Duplicate Detector (Model Cleaner)

A radical tool to combat material cost overruns caused by overlapping MEP elements (double ordering).

### Technical Features:
*   **Strict Overlap Verification**: The algorithm doesn't just find partial intersections; it checks if two elements share the exact same geometry (same central curve, same vertices) and the same dimensions.
*   **Automated Purge**: One-click deletion of identified redundant elements, with dynamic list refresh.

---

## 5. Module 4: BIM Data Audit & Compliance (Quality Control)

The latest major evolution of SmartBimPeï, this module acts as the "Data Police." It ensures the model's validity for the Operations & Maintenance (Facility Management / CMMS) and Handover (As-Built) phases.

> [!IMPORTANT]
> **"Central Brain" Innovation (Anti-False Positive)**
> The verification algorithm (`HasValidParameter`) was designed to replicate the intelligence of a human BIM Manager. When searching for a parameter (e.g., *Fire Rating* or *Structural Material*), **it first inspects the instance**. If not found or empty, **it automatically searches for this same information in the Type properties (`ElementType`)**. This guarantees the absence of false positives and prevents null-query crashes.

### Checks by Discipline (Trades):

1. **Architecture (Interiors & Openings)** *(Doors, Windows, Ceilings, Walls)*
   - Verification of the native **Mark** parameter for all elements.
   - Verification of **Fire Rating** (Compartmentation) on Walls and Doors.
   - Verification of **Manufacturer** and **Model** for Doors, Windows, and Ceilings (CMMS).

2. **Structure** *(Walls, Slabs, Beams, Columns)*
   - Verification of the **Mark** parameter.
   - Verification of **Structural Material** (Crucial for carbon footprint and load calculations).
   - Verification of **Fire Rating** (Compliance).

3. **HVAC (Ducts & Equipment)**
   - Verification of **Duct System** (Air Classification).
   - Verification of **Manufacturer** and **Model** on equipment (AHUs, Terminals).
   - **Spatial Location** (Room / Space) is mandatory for terminals to certify COBie reports.

4. **Plumbing (Pipes & Equipment)**
   - Verification of **Piping System** (e.g., Waste, Supply, DHW).
   - Verification against *Default* values in the Family & Type parameter.
   - Verification of **Manufacturer**, **Model**, and **Spatial Location** (Room/Space) for equipment and fixtures.

5. **Electrical (Power/Comms & Trays)**
   - Verification of the power **Panel** (for fixtures and equipment).
   - Verification of the **Mark** parameter for Main Boards and Comms equipment (Fire Alarm, Data).
   - Strict verification of **Manufacturer**, **Model**, and **Spatial Location**.

---

## 6. Module 5: Métré Express (Quantities & Pricing)

A direct bridge between 3D geometry and construction economics.

### Technical Features:
*   **Parametric Extraction**: Dynamic retrieval of real geometric values (Length, Area, Volume, Diameter) directly from the API, independent of native Revit schedules.
*   **Financial Valuation**: Cross-referencing extracted quantities with an integrated unit price library to obtain an instant total estimate.
*   **Interoperability**: Robust import/export function (CSV/Excel format) for data exchange with third-party quantity surveying software.

---

*This document certifies the cutting-edge technical capabilities of the SmartBimPeï suite, demonstrating expert mastery of the Revit API (C#), interface development (WPF), and the complex challenges of OpenBIM coordination and Facility Management.*
