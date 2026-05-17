# Part 4: AI Solution Design for a Business Problem

## Task 1: Choose a Business Domain
* **Selected Domain:** Manufacturing



## Task 2: Define the Business Problem
* **What problem is being solved?** The automated detection, classification, and grading of structural surface anomalies (including dents, scratches, and stains) on parts moving down a high-speed production line.
* **Who are the users or stakeholders?** Quality Assurance (QA) Engineers, Plant Floor Managers, Assembly Line Operators, and Operations Executives.
* **What is the current manual or traditional process?** Production facilities rely heavily on human technicians stationed physically along conveyor belts. These inspectors manually scan items passing by or look at raw, non-intelligent optical video feeds to pick out visual defects.
* **What are the limitations of the current process?** * **High Error Rates due to Fatigue:** Human inspectors experience rapid cognitive fatigue over a shift, missing fine micro-defects or surface anomalies over time.
    * **Throughput Bottlenecks:** Assembly lines are constrained to move only as fast as a human eye can reliably judge each part, capping daily output.
    * **Subjective Evaluation:** Defect standards are highly variable across individual workers, leading to inconsistent grading, false alarms, or missed faults.
    * **High Operational Scale Costs:** Scaling up factory output requires a linear increase in QA headcount, which balloon manufacturing operational overhead.



## Task 3: Identify the AI Task Type
* **Selected AI Task Type:** Image Classification
* **Why this task type is suitable:** The target objective is to evaluate a cropped product image captured by factory edge-cameras and immediately assign a single explicit categorical label from a predefined set (`normal`, `dent`, `scratch`, or `stain`). Since each picture represents a distinct physical part evaluate to determine its singular health state, structuring this as a multi-class image classification task is highly efficient and directly maps to the downstream sorting mechanisms.


## Task 4: Data Requirement Plan
* **Type of data needed:** High-resolution digital imagery taken under uniform, controlled factory lighting conditions.
* **Structured or unstructured data:** Unstructured data (Images).
* **Input features:** 2D raw pixel matrices, standardized and reshaped to a uniform resolution of $128 \times 128$ pixels across 3 color channels (RGB).
* **Target variable or labels:** Categorical labels mapping to a text-encoded vector: `[0: normal, 1: dent, 2: scratch, 3: stain]`.
* **Data collection method:** Automated industrial overhead cameras mounted directly above key conveyor junctions, triggered automatically by physical proximity sensors whenever an object breaks the path sensor beam.
* **Data quality risks:**
    * **Lighting Fluctuations:** Shifts in external factory ambient lighting can generate glare, rendering surface anomalies invisible to the lens.
    * **Lens Contamination:** Dust particles or machine oil smudging the lens elements could introduce visual artifacts that trick models into predicting false "stains."
    * **Label Mismatch:** Initial manual labeling errors when sorting historical training baselines can inject incorrect ground truth labels.



## Task 5: Model Recommendation
* **Recommended Architecture:** Convolutional Neural Network (CNN) 
* **Why the selected model is appropriate:** * **Spatial Hierarchy Retention:** CNNs make use of localized sliding convolutional filters that actively preserve spatial pixel neighborhoods. This lets the network map out low-level features (like sharp edges or line cracks for scratches) and bundle them into high-level features (like regional discoloration patches for stains).
    * **Parameter Minimization:** By employing spatial pooling operations and weight sharing, a standard CNN drastically minimizes the total parameter count compared to a standard dense feed-forward network, keeping the model from overfitting on limited industrial samples.
    * **Edge-Deployable Inference:** A streamlined 3-layer CNN architecture maintains a lightweight memory footprint, processing frames within milliseconds to enable immediate automated sorting gates without slowing factory line speeds.



## Task 6: Evaluation Plan
* **Technical Metrics:** * **Defect Recall (Sensitivity):** Crucial for minimizing missed defects; it measures what percentage of total actual anomalies were successfully flagged by the CNN model.
    * **Precision:** Ensures the system minimizes false positives on clean (`normal`) items, which would otherwise disrupt operational flow with unnecessary re-inspections.
    * **Macro F1-Score:** Balances structural performance evaluation across all 4 specific target folder labels equally, countering minor variations in image availability.
* **Business Metrics:** * **Manual Processing Hours Saved:** Quantifying the monthly reduction in workforce hours dedicated to routine visual inspection.
    * **Defect Escape Rate:** Tracking the decrease in defective components leaking past checkpoints and making it into final product shipments.
    * **Inspection Cycle Time:** Measuring the drop in average seconds required to completely evaluate an active product unit.
* **Possible Failure Cases:** Severe camera vibration knocking the frame out of focus, creating generalized blurry inputs that lead the network to misclassify structural lines.
* **Human Review / Validation Process:** A dedicated human-in-the-loop review architecture. Any sample where the model's highest Softmax output prediction confidence scores sit underneath an $85\%$ threshold is pulled from the main line and routed to an inspection siding for physical verification by a QA Engineer.



## Task 7: Responsible AI Considerations
* **Data Selection Bias:** If the baseline dataset images only contain silver metallic components, the model will output poor accuracy metrics when evaluating dark plastic or coated variants. Training sets must span all material profiles.
* **Impact of Misclassifications:** False negative errors can allow defective parts to pass through to crucial safety sub-assemblies, risking downstream structural failures.
* **Privacy Controls:** Even though imagery targets product surfaces, protocols must verify that surrounding background paths do not accidentally store clear frames containing operators' faces or ID badges.
* **Automation Over-reliance:** Plant crews might stop running periodic mechanical line evaluations under the assumption that the AI layer will catch structural line drifts independently, allowing slowly compounding line errors to go unchecked.
* **Human-in-the-Loop Safeguards:** AI diagnostic metrics must operate as a secondary triage assistant for QA operators rather than an unmonitored autonomous inspector, leaving human operators with final override control.



## Task 8: Final Solution Summary

| Section | Solution Summary Details |
| :--- | :--- |
| **Problem** | Quality control relies on manual visual checks that are slowed down by human fatigue, causing high operational variance and expensive defect escapes. |
| **Proposed AI Solution** | An automated computer vision triage process run directly on line-edge devices using a custom Convolutional Neural Network (CNN) to instantly screen passing items. |
| **Required Data** | High-resolution overhead RGB surface images ($96 \times 96$ standard upsampled to $128 \times 128$), labeled across four target classes: `normal`, `dent`, `scratch`, and `stain`. |
| **Model Recommendation** | A deep 2D Convolutional Neural Network architecture incorporating Max-Pooling layers for spatial reduction, ReLU activations for non-linear feature mapping, and a dense Dropout-regularized classification head. |
| **Expected Business Impact** | Reduction in manual visual processing hours by up to $60\%$, standardized defect grading criteria, faster unit cycle times, and a decrease in consumer product return rates. |
| **Risks & Mitigation Plan** | **Risk:** Industrial lens smudging or camera vibration creating false defect predictions. <br>**Mitigation:** Regular physical camera calibration loops combined with automated edge-case routing to human QA inspectors when prediction confidence falls beneath $85\%$. |

