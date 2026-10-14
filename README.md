# Eliminarea reflexiilor speculare și a strălucirii (glare) din imagini prin votare și fuziune multi-algoritm

## 1. Descrierea Problemei și Motivație
Reflexiile speculare (specular highlights / glare) apar atunci când lumina se reflectă direct de pe suprafețe lucioase, umede, metalice sau lentile de ochelari, saturând senzorul camerei. Aceste regiuni pierd informația de culoare și textură a obiectului din spate, afectând atât calitatea vizuală a fotografiei, cât și performanța algoritmilor de vedere artificială (OCR, segmentare, recunoaștere de obiecte).

Deoarece refacerea informației pierdute sub o reflexie speculară este o problemă prost pusă (ill-posed), niciun algoritm individual nu oferă rezultate optime în toate scenariile (obiecte texturate vs. suprafețe uniforme, iluminare complexă). Acest proiect propune o abordare bazată pe **procesare prin votare și fuziune**, combinând metode fizice (bazate pe modelul de reflexie dicromatică), metode clasice de inpainting și modele de învățare profundă.

## 2. Arhitectura Propusă (Procesare prin Votare)
Sistemul este împărțit în două etape principale, fiecare utilizând un mecanism de agregare/votare:

### Etapa A: Detectarea măștii speculare (Votare la nivel de mască)
Combinarea măștilor binare/probabilistice generate de mai mulți detectori:
1. **Praguri în spații cromatice (HSV / CIE Lab):** Identificarea pixelilor cu saturație scăzută și luminanță ridicată.
2. **Modelul de Reflexie Dicromatică (DRM) / Specular-Free Image:** Separarea componentei difuze de cea speculară pe baza cromaticității.
3. **Detector bazat pe gradienți și morfologie matematică:** Izolarea zonelor de strălucire intensă.
* **Mecanism de votare:** Vot majoritar ponderat / algoritmul STAPLE pentru obținerea unei măști de consens robuste, reducând alarmele false pe obiecte natural albe.

### Etapa B: Reconstrucția suprafeței și eliminarea strălucirii (Fuziune la nivel de imagine)
Aplicarea mai multor algoritmi de restaurare pe zonele indicate de masca de consens:
1. **Atenuare cromatică (Shen / Tan Dichromatic Separation):** Recalcularea culorii difuze pentru reflexii semi-transparente.
2. **Inpainting clasic (Telea / Navier-Stokes / Criminisi Exemplar-Based):** Propagarea texturii din vecinătatea regiunii saturate.
3. **Model Pre-antrenat de Deep Inpainting / Specular Removal (ex. LaMa / TSHRNet):** Reconstrucție semantică a zonelor puternic saturate.
* **Mecanism de combinare/votare:** Fuziune multi-scală (piramide Laplaciene) și ponderare bazată pe hărți de încredere locale (continuitatea gradienților la frontieră și consistența cromatică).

## 3. Planificarea pe Sprint-uri
* **Sprint 1 (Săptămâna 4):** Studiu bibliografic, selecția seturilor de date cu ground-truth (ex. *SHIQ - Specular Highlight Image Quadruplets*, *PSD*, *WHU-Specular*) și definirea arhitecturii detaliate a sistemului.
* **Sprint 2 (Săptămâna 8):** Implementarea și integrarea algoritmilor individuali de detecție a măștii speculare și a metodelor de reconstrucție/inpainting (intrările pentru procesul de votare).
* **Sprint 3 (Săptămâna 12):** Dezvoltarea algoritmului de votare pentru mască și a modulului de fuziune a imaginilor restaurate; optimizarea ponderilor.
* **Sprint 4 (Săptămâna 14):** Evaluare cantitativă (PSNR, SSIM, LPIPS) și calitativă (comparație între metodele individuale vs. rezultatul votat), finalizarea aplicației demonstrator și redactarea articolului științific.

## 4. Tehnologii și Metrici de Evaluare
* **Limbaj & Biblioteci:** Python, OpenCV, NumPy, SciPy, PyTorch, Scikit-Image.
* **Metrici:** PSNR (Peak Signal-to-Noise Ratio), SSIM (Structural Similarity Index), Dice/IoU (pentru acuratețea măștii speculare).
