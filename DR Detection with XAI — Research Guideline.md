**DR · XAI Guideline**

# Diabetic Retinopathy Detection with Explainable AI

বাংলাদেশের হাসপাতাল থেকে data collection-এর আগে যা যাচাই করতে হবে, সেই technical, clinical, ethical ও research requirement-এর পূর্ণ guideline। একটি grade বেছে নিন:

DR grade আর diabetic macular edema (DME) এক নয়; DME scope-এ থাকলে আলাদা clinical label লাগবে। চূড়ান্ত label হবে হাসপাতালের অনুমোদিত grading protocol ও যোগ্য grader অনুযায়ী।

## ১. Research objective

প্রথমে scope পরিষ্কার করুন।

#### Classification

Fundus image থেকে ৫টি clinical grade শনাক্ত।

#### Model evaluation

উপযুক্ত deep learning model-এর performance মূল্যায়ন।

#### Explainability

Grad-CAM বা অন্য XAI দিয়ে prediction ব্যাখ্যা।

#### Expert comparison

সম্ভব হলে lesion annotation-এর সঙ্গে explanation তুলনা।

#### Local validity

বাংলাদেশের clinical image-এ কার্যকারিতা ও সীমাবদ্ধতা।

## ২. কতগুলো image দরকার?

৫০০ বা ১,০০০ per class একটি planning target, universal scientific minimum নয়।

Usable image / class**500**

Train %**70**

Validation %**15**

Test % = বাকি অংশ। Preset:

2,500 মোট usable image (৫ class)

Image count-এর পাশাপাশি unique patient count, প্রতি class-এ patient, চোখ অনুযায়ী image, repeat visit ও QC-তে বাদ পড়া image রাখুন। একই রোগীর একাধিক ছবি independent patient নয়। Patient-level grouping-এর পর প্রকৃত split সংখ্যা ভিন্ন হতে পারে।

## ৩. হাসপাতাল থেকে কী ধরনের image?

Native resolution ও original file সংরক্ষণই অগ্রাধিকার।

## ৪. Image quality control

প্রতিটি ছবির জন্য নির্ধারিত procedure; বাদ গেলে কত ও কেন তা report করুন।

| Criterion | কী পরীক্ষা করবেন |
| --- | --- |
| Focus | Retina ও blood vessel যথেষ্ট স্পষ্ট কি না |
| Illumination | অতিরিক্ত উজ্জ্বল বা অন্ধকার কি না |
| Field of view | প্রয়োজনীয় retinal area দৃশ্যমান কি না |
| Occlusion | Eyelash, reflection ইত্যাদিতে retina ঢাকা কি না |
| Color | অস্বাভাবিক color cast বা processing artifact |
| Resolution | প্রয়োজনীয় clinical detail আছে কি না |
| Duplicate | একই বা প্রায় একই ছবির পুনরাবৃত্তি |
| Gradability | বিশেষজ্ঞ নির্ভরযোগ্যভাবে grade দিতে পারেন কি না |

ঝুঁকি: No DR ছবি পরিষ্কার আর Severe DR ছবি ঝাপসা হলে model রোগের বদলে image quality শিখতে পারে। প্রতিটি class-এ quality distribution তুলনা করুন। Ungradable image-কে No DR label দেবেন না।

## ৫. Clinical label ও grading protocol

এখানে আপস নয়। হাসপাতালকে এই ৬টি প্রশ্ন করুন:

**Double grading ও adjudication:** সম্ভব হলে একটি subset দুইজন qualified grader স্বাধীনভাবে grade করবেন; disagreement rate ও final label কীভাবে স্থির হলো লিখুন। Agreement-এ Cohen’s kappa বা weighted kappa ব্যবহার করা যায়। সব image double grade না হলে sample-based review করে কত শতাংশ যাচাই হলো report করুন। হাসপাতালের patient-level diagnosis স্বয়ংক্রিয়ভাবে image-level label নয়।

## ৬. Metadata CSV structure

প্রতিটি row একটি image। Header copy করে হাসপাতালকে পাঠাতে পারেন।

#### Privacy ও security

- নাম, ফোন, ঠিকানা, NID, registration number modeling CSV-তে নয়।
- Patient ID pseudonymize; re-identification key আলাদা, প্রতিষ্ঠানের নিয়ন্ত্রণে।
- EXIF/embedded metadata পরীক্ষা করুন।
- Access, storage, retention, sharing নিয়ম আগে ঠিক করুন; checksum রাখুন।

#### কেন patient ID জরুরি

Patient ID ছাড়া patient-level split ও repeat-image leakage প্রতিরোধ অনেক কঠিন। এটি আগেই সমাধান করুন।

## ৭. Class imbalance

কোনো balancing method দুর্বল label, কম unique patient বা নিম্নমানের image-এর বিকল্প নয়।

#### Class-weighted loss

Training-এ minority class-এর ভুলকে বেশি গুরুত্ব।

#### Balanced sampling

Batch-এ class representation নিয়ন্ত্রণ।

#### Augmentation

Clinically reasonable flip, modest rotation, brightness/contrast; diagnosis-related তথ্য বিকৃত না হলে।

#### Oversampling

শুধু training set-এ; duplicate-কে নতুন independent image গণনা করবেন না।

#### Synthetic data

স্পষ্টভাবে চিহ্নিত করুন; real, untouched test set-এ যাচাই।

হিসাব রাখুন: class-wise count/percentage, unique patient, gradable/ungradable, age group (অনুমোদিত হলে), camera/site/visit অনুযায়ী distribution।

## ৮. Split ও external validation

সবচেয়ে গুরুত্বপূর্ণ নিয়ম: patient-level split। (Split-এর অনুপাত ২ নম্বর section-এর planner-এ দেখুন।)

Test set-এ augmentation, model selection বা hyperparameter tuning নয়। Patient ID না পেলে হাসপাতালের সঙ্গে privacy-preserving উপায় ঠিক করুন। External validation internal test-এর বিকল্প নয়, অতিরিক্ত ধাপ; grading scale, camera ও population-এর পার্থক্য নথিভুক্ত করুন। অন্য dataset-এ model খারাপ করাও গুরুত্বপূর্ণ ফল।

## ৯. Model ও metrics

আগে reproducible baseline (Basic CNN), তারপর ResNet, EfficientNet বা অন্য উপযুক্ত architecture। Pretrained হলে weights source ও fine-tuning নথিভুক্ত করুন।

| Metric | কেন |
| --- | --- |
| Accuracy | সব prediction-এর মধ্যে সঠিকের অনুপাত |
| Macro-F1 | প্রতিটি class-কে সমান গুরুত্ব |
| Per-class precision | Predict করা ছবির কত অংশ সঠিক |
| Per-class recall / sensitivity | প্রকৃত ছবির কত অংশ শনাক্ত |
| Specificity | Negative case সঠিক চেনার ক্ষমতা |
| Confusion matrix | কোন grade কোনটির সঙ্গে গুলিয়ে যাচ্ছে |
| ROC-AUC | One-vs-rest বা binary setting-এ discrimination |
| Quadratic weighted kappa | Ordered grade-এর agreement |

Multiclass-এ sensitivity/specificity/AUC কীভাবে গণনা হলো লিখুন। Screening দাবি করলে operating threshold ও false-negative-এর গুরুত্ব বিবেচনা করুন। Confidence interval দিন; একই রোগীর একাধিক ছবি থাকলে patient-level dependence ধরুন।

## ১০. Explainable AI validation

শুধু heatmap বানানো যথেষ্ট নয়; কীভাবে মূল্যায়ন করবেন তা data collection-এর সময়ই ঠিক করুন।

#### Level 1 · Grad-CAM analysis

বিভিন্ন class, correct/misclassified, confidence level-এর উদাহরণে দেখুন model retina-র relevant অঞ্চলে focus করছে কি না।

#### Level 2 · Expert-annotated subset

Microaneurysm, hemorrhage, exudate ইত্যাদির mask বা bounding box, representative subset-এ।

#### Level 3 · Quantitative

Ground truth থাকলে IoU/Dice; সব heatmap-এর জন্য সমান উপযুক্ত নয়, তাই method ও metric-এর মিল ব্যাখ্যা করুন।

#### কী সংগ্রহ করবেন

- Expert lesion annotation ও তার documentation
- Predicted class ও confidence
- XAI method, layer, configuration
- Heatmap-এর preprocessing ও coordinate mapping
- Correct ও incorrect prediction-এর explanation
- Stability/sensitivity test পরিকল্পনা

#### সতর্কতা

- সুন্দর heatmap মানেই clinically faithful নয়।
- Expert annotation ছাড়া lesion localization প্রমাণিত বলবেন না।
- আলাদা clinical validation ছাড়া চিকিৎসা-সিদ্ধান্তের সক্ষমতা দাবি নয়।
- Grad-CAM-এর পাশে Integrated Gradients তুলনা করা যায়।

## ১১. Lesion annotation চাওয়া

সব ছবির pixel-level annotation না চেয়ে আগে representative subset।

- প্রতিটি grade থেকে উপযুক্ত subset-এ annotation সম্ভব কি না
- Pixel mask, bounding box, নাকি lesion presence/absence
- কোন lesion type; কে করেছেন; কীভাবে যাচাই
- Image ID-র সঙ্গে annotation-এর সংযোগ
- Annotation ও গবেষণায় ব্যবহারের অনুমোদন

Feasibility pilot উদাহরণ: প্রতি grade থেকে ৫০–১০০ ছবি (বাধ্যতামূলক সংখ্যা নয়)। Annotation না পেলেও classification + qualitative XAI সম্ভব, তবে quantitative localization দাবি সীমিত রাখতে হবে।

## ১২. Ethics, consent ও data-use agreement

হাসপাতালের অনুমোদিত research process অনুসরণ করুন।

Consent waiver বা ethics exemption নিজে থেকে ধরে নেবেন না; যথাযথ কর্তৃপক্ষের সিদ্ধান্ত লাগবে।

## ১৩. Folder structure

Image, metadata ও annotation-এ consistent image ID। অনুমোদন ছাড়া clinical image public repository বা version-control-এ আপলোড নয়।

```
DR_XAI_Research/
├── README.md
├── data_dictionary.csv
├── metadata/        image_metadata · label_metadata · split_manifest (.csv)
├── images_original/ batch_001/ batch_002/
├── annotations/     lesion_masks/ · annotation_metadata.csv
├── quality_control/ qc_results.csv · exclusion_log.csv
├── processed_data/  preprocessing_config.yaml
├── src/             preprocessing · train · evaluate · explain (.py)
└── results/         metrics/ · confusion_matrices/ · xai_examples/
```

## ১৪. হাসপাতালের কাছে একবারে কী চাইবেন

টিক দিন, progress দেখুন, শেষে বাকি অংশ copy করে হাসপাতালকে পাঠান।

0/23সম্পন্ন

## ১৫. Data collection থেকে paper

১২ ধাপের workflow; শেষ করা ধাপে টিক দিন।

## ১৬. Paper-এ কী report করবেন

CLAIM (medical imaging AI) ও STARD-AI অনুসরণের উপযোগিতা যাচাই করুন; এগুলো স্বচ্ছতা বাড়ায়, কিন্তু dataset বা model validity নিশ্চিত করে না।

## চূড়ান্ত সুপারিশ

1. পাঁচ grade-সহ clinically verified real fundus image; প্রাথমিক 2,500, বর্ধিত 5,000 usable image বিবেচনা (বাস্তব সংখ্যা clinical data ও statistical need অনুযায়ী)।
2. Image count-এর পাশে unique patient, left/right eye ও repeat-visit তথ্য।
3. Original file, resolution, format, metadata, image-label mapping সংরক্ষণ।
4. Grading scale, quality criteria, disagreement resolution আগে নিশ্চিত।
5. Ethics, data-use permission ও privacy মেনে সংগ্রহ; patient-level split।
6. Imbalance, quality ও performance মূল্যায়ন; XAI + expert-annotated subset; সম্ভব হলে external validation; সব limitation প্রকাশ।

সবচেয়ে গুরুত্বপূর্ণ: image, clinical label, pseudonymous patient ID, grading protocol, quality information ও অনুমোদিত metadata একসঙ্গে সংগ্রহ করুন। এতে পুনরায় data সংগ্রহ কমতে পারে, যদিও সম্পূর্ণ এড়ানো নিশ্চিত নয়।

অগ্রগতি (checklist ও workflow) এই browser-এ সংরক্ষিত থাকে।