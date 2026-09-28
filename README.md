# Lab: Stored DOM XSS

## 📚 Studijní materiál
Tento repozitář slouží jako můj osobní studijní zápisník a přehled řešení laboratoří z platformy **PortSwigger Web Security Academy**. Dokumentuji zde postupy, zranitelnosti a způsob jejich exploitationu pro budoucí reference a rozvoj znalostí v oblasti kybernetické bezpečnosti.

---

## 🛠️ Postup řešení laboratoře

1. **Průzkum aplikace:** Otevřel jsem si detail vybraného příspěvku (blogu), kde se nachází sekce pro vkládání komentářů.
2. **Injekce payloadu do formuláře:** Do komentáře jsem vyplnil potřebné údaje:
   * **Comment:** `<><img src=1 onerror=alert(1)>` (zranitelný vstup obsahující payload).
   * **Name & Email:** Libovolné vymyslené údaje.
   * **Website:** `https://securitycode.org` (jako adresu webu jsem použil tuto konkrétní hodnotu, kterou běžně vídám a čerpám z návodů na YouTube).
3. **Odeslání a návrat zpět:** Formulář jsem odeslal. Poté bylo nutné kliknout na tlačítko pro návrat zpět (Back), aby se komentář správně načetl a vykreslil v kontextu stránky.
4. **Dokončení:** Jakmile se stránka vrátila a komentář se zobrazil, prohlížeč vyhodnotil chybu obrázku a spustil skript, čímž byla laboratoř úspěšně vyřešena.

---

## 🔍 Technické vysvětlení a princip zranitelnosti

Tento typ laboratoře demonstruje **Stored DOM-based XSS** (uložený DOM XSS). Na rozdíl od reflected XSS se škodlivý kód nejprve uloží do databáze aplikace (například v komentářích) a následně se servíruje všem uživatelům, kteří si stránku zobrazí.

> ### Proč použitý payload fungoval?
> * **HTML tag `<img src=1>`:** Využili jsme tag obrázku s neplatnou cestou (`src=1`), což schválně vyvolá chybu při načítání obrázku.
> * **Událost `onerror=alert(1)`:** Jakmile nastane chyba při načítání obrázku, prohlížeč okamžitě spustí JavaScriptový kód uvnitř atributu `onerror` – v tomto případě vyskakovací okno `alert(1)`.
> * **Uložení na serveru:** Jelikož aplikace uživatelský vstup řádně neošetřila před uložením do databáze, stala se trvassle zranitelnou pro každého, kdo si komentář přečte.