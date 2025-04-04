# **Schritt 1: Was ist PL/SQL und die grundlegende Struktur?**

**Was ist PL/SQL?**

* PL/SQL steht für **P**rocedural **L**anguage extension to **SQL**.
* Es ist Oracles prozedurale Spracherweiterung für SQL. Das bedeutet, du kannst die Mächtigkeit von SQL (Datenabfrage und -manipulation) mit prozeduralen Konstrukten wie Variablen, Bedingungen (IF/THEN/ELSE), Schleifen (Loops) und Fehlerbehandlung kombinieren.
* PL/SQL-Code wird direkt **innerhalb der Oracle-Datenbank** ausgeführt. Das ist effizient, da die Logik nahe an den Daten liegt und Netzwerklast reduziert wird.
* Du kannst damit u.a. Stored Procedures, Functions, Trigger und Packages erstellen.

**Der grundlegende PL/SQL-Block (Anonymer Block)**

Der einfachste Baustein in PL/SQL ist ein sogenannter "anonymer Block". Er hat keine Namen und wird direkt ausgeführt. Er hat folgende Struktur:

```sql
DECLARE
  -- Optional: Deklarationsteil
  -- Hier deklarierst du Variablen, Konstanten, Cursor, Exceptions etc.,
  -- die du im Block verwenden möchtest.
  -- Wenn du nichts deklarieren musst, kannst du diesen Teil weglassen.

BEGIN
  -- Ausführungsteil (Muss vorhanden sein)
  -- Hier schreibst du die eigentliche Logik, die ausgeführt werden soll.
  -- Das können SQL-Anweisungen (SELECT, INSERT, UPDATE, DELETE) oder
  -- PL/SQL-spezifische Anweisungen (Zuweisungen, IF, LOOPs etc.) sein.

EXCEPTION
  -- Optional: Exception-Handler-Teil
  -- Hier definierst du, wie auf Fehler (Exceptions) reagiert werden soll,
  -- die im BEGIN-Teil auftreten könnten.
  -- Wenn du keine spezielle Fehlerbehandlung brauchst, kannst du diesen Teil weglassen.

END;
/ -- Dieses Slash teilt dem SQL-Client (wie SQL*Plus oder SQL Developer) mit,
  -- dass der PL/SQL-Block zu Ende ist und ausgeführt werden soll.
```

**Erstes Beispiel: "Hallo Welt"**

Lass uns einen einfachen anonymen Block erstellen, der nur eine Nachricht ausgibt.

* **Wichtig:** Damit du die Ausgabe von `DBMS_OUTPUT.PUT_LINE` in den meisten SQL-Tools (wie SQL*Plus oder SQL Developer) sehen kannst, musst du **vorher** einmal pro Sitzung den Befehl `SET SERVEROUTPUT ON;` ausführen.

```sql
-- (Optional, aber meist nötig, um die Ausgabe zu sehen - führe dies zuerst aus)
SET SERVEROUTPUT ON;

-- Beginn des anonymen PL/SQL-Blocks
DECLARE
  -- In diesem einfachen Beispiel brauchen wir keine Variablen,
  -- daher ist der DECLARE-Teil leer (oder könnte ganz weggelassen werden).
  v_nachricht VARCHAR2(100) := 'Hallo Welt aus PL/SQL!'; -- Beispiel für eine Variable, falls benötigt

BEGIN
  -- Ausführungsteil: Gib eine Nachricht auf dem Bildschirm aus.
  -- DBMS_OUTPUT ist ein von Oracle bereitgestelltes Package (eine Sammlung von Prozeduren/Funktionen).
  -- PUT_LINE ist eine Prozedur in diesem Package, die eine Zeile Text ausgibt.
  DBMS_OUTPUT.PUT_LINE(v_nachricht); -- Gibt den Inhalt der Variablen aus
  DBMS_OUTPUT.PUT_LINE('Dies ist ein einfaches Beispiel.'); -- Gibt einen festen Text aus

  -- Hier könnten weitere Anweisungen folgen...

-- Kein EXCEPTION-Teil in diesem einfachen Beispiel.

END; -- Ende des PL/SQL-Blocks
/ -- Führe den Block aus
```

**Zusammenfassung Schritt 1:**

* Du weißt jetzt, was PL/SQL ist (Oracles prozedurale Erweiterung zu SQL).
* Du kennst die grundlegende Struktur eines PL/SQL-Blocks (DECLARE, BEGIN, EXCEPTION, END).
* Du hast gesehen, wie man einen einfachen anonymen Block schreibt und mit `DBMS_OUTPUT.PUT_LINE` eine Ausgabe erzeugt.

---

# **Schritt 2: Variablen, Konstanten und grundlegende Datentypen**

Im `DECLARE`-Teil eines PL/SQL-Blocks definierst du die Variablen und Konstanten, die du im `BEGIN`-Teil verwenden möchtest. Eine Variable ist wie ein Behälter, der einen Wert eines bestimmten Typs speichern kann.

**Deklaration von Variablen**

Die Syntax zur Deklaration einer Variablen sieht so aus:

```sql
variablen_name [CONSTANT] datentyp [NOT NULL] [:= | DEFAULT initialwert];
```

* `variablen_name`: Der Name, den du der Variablen gibst (muss den Oracle-Namenskonventionen folgen, z.B. nicht mit Zahl beginnen, keine Leerzeichen).
* `CONSTANT` (optional): Wenn du dieses Schlüsselwort angibst, erstellst du eine Konstante. Ihr Wert muss bei der Deklaration zugewiesen werden und kann später nicht mehr geändert werden.
* `datentyp`: Der Typ der Daten, die die Variable speichern kann (z.B. Text, Zahl, Datum).
* `NOT NULL` (optional): Wenn angegeben, muss die Variable immer einen Wert haben; sie darf nicht `NULL` sein. Du musst ihr dann bei der Deklaration einen Initialwert geben.
* `:=` oder `DEFAULT initialwert` (optional): Hiermit kannst du der Variablen direkt bei der Deklaration einen Anfangswert zuweisen. `:=` ist der Zuweisungsoperator in PL/SQL.

**Wichtige Grund-Datentypen in Oracle PL/SQL**

* `VARCHAR2(n)`: Speichert Zeichenketten (Text) variabler Länge bis zu einer maximalen Länge von `n` Bytes oder Zeichen (abhängig von DB-Einstellungen). *Dies ist der am häufigsten verwendete Typ für Text.*
* `CHAR(n)`: Speichert Zeichenketten fester Länge `n`. Kürzere Zeichenketten werden mit Leerzeichen aufgefüllt. *Meist wird `VARCHAR2` bevorzugt.*
* `NUMBER`: Speichert numerische Werte (ganze Zahlen und Dezimalzahlen). Man kann optional Genauigkeit und Skalierung angeben, z.B. `NUMBER(p, s)`, wobei `p` die Gesamtzahl der Ziffern (Precision) und `s` die Anzahl der Nachkommastellen (Scale) ist. `NUMBER` ohne Angabe kann sehr große/präzise Zahlen speichern. `INTEGER` ist ein Subtyp von `NUMBER` für ganze Zahlen.
* `DATE`: Speichert Datums- und Zeitwerte (Jahr, Monat, Tag, Stunde, Minute, Sekunde).
* `BOOLEAN`: Kann die Werte `TRUE`, `FALSE` oder `NULL` speichern. Kann nicht direkt in SQL-Anweisungen verwendet oder in Datenbankspalten gespeichert werden, ist aber sehr nützlich für die Ablaufsteuerung in PL/SQL.
* `TIMESTAMP`: Ähnlich wie `DATE`, speichert aber zusätzlich Sekundenbruchteile und optional Zeitzoneninformationen.

**Anchored Declaration (`%TYPE`)**

Eine sehr nützliche Technik ist die "verankerte" Deklaration mit `%TYPE`. Damit kannst du eine Variable deklarieren, die denselben Datentyp hat wie eine bestimmte Datenbankspalte oder eine andere bereits deklarierte Variable.

Syntax: `variable_name tabelle.spalte%TYPE;`
Syntax: `andere_variable variable_name%TYPE;`

* **Vorteil:** Wenn sich der Datentyp der Spalte in der Datenbank ändert (z.B. von `NUMBER` zu `VARCHAR2` oder die Länge eines `VARCHAR2` erhöht wird), passt sich der Datentyp deiner PL/SQL-Variable automatisch an, ohne dass du den PL/SQL-Code ändern musst. Das macht deinen Code robuster und wartungsfreundlicher.

**Beispiel:**

```sql
-- Serveroutput einschalten, um die Ausgaben zu sehen
SET SERVEROUTPUT ON;

DECLARE
  -- *** Variablen-Deklarationen ***

  -- Eine Textvariable mit maximal 50 Zeichen, initialisiert mit einem Wert
  v_begruessung   VARCHAR2(50) := 'Herzlich Willkommen';

  -- Eine Ganzzahlvariable ohne Initialwert (ist standardmäßig NULL)
  v_anzahl        INTEGER;

  -- Eine Dezimalzahlvariable für einen Preis (max. 10 Ziffern gesamt, 2 davon nach dem Komma)
  v_preis         NUMBER(10, 2) DEFAULT 99.95; -- Initialisierung mit DEFAULT

  -- Eine Datumsvariable, initialisiert mit dem aktuellen Datum und der aktuellen Zeit
  v_heute         DATE := SYSDATE; -- SYSDATE ist eine Oracle-Funktion für das aktuelle DB-Datum/Zeit

  -- Eine boolesche Variable, initialisiert mit FALSE
  v_ist_aktiv     BOOLEAN := FALSE;

  -- Eine Variable, die den Datentyp einer (hypothetischen) Spalte 'gehalt'
  -- in einer (hypothetischen) Tabelle 'mitarbeiter' übernimmt.
  -- Das ist sehr nützlich, um sicherzustellen, dass die Variable immer zum Spaltentyp passt.
  v_gehalt        mitarbeiter.gehalt%TYPE; -- Angenommen, es gibt eine Tabelle mitarbeiter mit Spalte gehalt

  -- Eine Variable, die den Typ einer anderen Variablen übernimmt
  v_gehalt_kopie  v_gehalt%TYPE;

  -- *** Konstanten-Deklaration ***
  -- Eine numerische Konstante für die Mehrwertsteuer. Muss initialisiert werden.
  c_mwst_satz     CONSTANT NUMBER := 0.20; -- 20%

BEGIN
  -- *** Ausführungsteil: Arbeiten mit den Variablen ***

  -- Einer Variablen einen Wert zuweisen
  v_anzahl := 10;

  -- Den Wert von v_gehalt setzen (hier nur ein Beispielwert)
  v_gehalt := 3000;

  -- Den Wert einer Variablen ändern
  v_begruessung := v_begruessung || ' zu PL/SQL!'; -- || ist der Verkettungsoperator für Strings

  -- Einfache Berechnungen
  v_preis := v_preis * (1 + c_mwst_satz); -- Bruttopreis berechnen

  -- Den Wert der booleschen Variable ändern
  IF v_anzahl > 5 THEN
    v_ist_aktiv := TRUE; -- Beispiel für eine Bedingung (IF lernen wir später genauer)
  END IF;

  -- Werte ausgeben
  DBMS_OUTPUT.PUT_LINE('Begrüßung: ' || v_begruessung);
  DBMS_OUTPUT.PUT_LINE('Anzahl: ' || v_anzahl);
  DBMS_OUTPUT.PUT_LINE('Heutiges Datum: ' || TO_CHAR(v_heute, 'DD.MM.YYYY HH24:MI:SS')); -- Datum muss für die Ausgabe oft in Text konvertiert werden
  DBMS_OUTPUT.PUT_LINE('Bruttopreis: ' || v_preis);
  DBMS_OUTPUT.PUT_LINE('MwSt-Satz (Konstante): ' || c_mwst_satz);
  DBMS_OUTPUT.PUT_LINE('Gehalt (aus %TYPE): ' || v_gehalt);

  -- Boolesche Werte können nicht direkt mit PUT_LINE ausgegeben werden,
  -- wir brauchen eine Hilfskonstruktion (z.B. CASE oder IF):
  DBMS_OUTPUT.PUT_LINE('Ist aktiv: ' || CASE WHEN v_ist_aktiv THEN 'Ja' ELSE 'Nein' END);

  -- Versuch, eine Konstante zu ändern (führt zu einem Kompilierfehler!)
  -- c_mwst_satz := 0.19; -- << Diese Zeile würde einen Fehler verursachen!

END;
/
```

**Zusammenfassung Schritt 2:**

* Du weißt, wie man Variablen und Konstanten im `DECLARE`-Teil deklariert.
* Du kennst die wichtigsten grundlegenden Datentypen (`VARCHAR2`, `NUMBER`, `DATE`, `BOOLEAN`).
* Du kannst Variablen Initialwerte zuweisen (`:=` oder `DEFAULT`).
* Du hast den Zuweisungsoperator `:=` gesehen.
* Du verstehst das Konzept und den Vorteil der verankerten Deklaration mit `%TYPE`.
* Du hast gesehen, wie man Variablen im `BEGIN`-Teil Werte zuweist und sie z.B. mit `DBMS_OUTPUT.PUT_LINE` ausgibt (wobei Datentypkonvertierungen nötig sein können, z.B. bei `DATE` oder `BOOLEAN`).

---

> Okay, jetzt wird es spannend: Wir integrieren SQL-Anweisungen in unseren PL/SQL-Code.

**Schritt 3: SQL in PL/SQL verwenden (SELECT INTO und grundlegende Fehlerbehandlung)**

Du kannst die meisten SQL-Anweisungen (wie `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `MERGE`) direkt in den `BEGIN`-Teil deines PL/SQL-Blocks schreiben.

**Daten aus der Datenbank lesen: `SELECT INTO`**

Eine der häufigsten Aufgaben ist es, Daten aus einer Tabelle zu lesen und in PL/SQL-Variablen zu speichern. Wenn du erwartest, dass deine Abfrage **genau eine Zeile** zurückliefert, verwendest du die `SELECT INTO`-Anweisung.

**Syntax:**

```sql
SELECT spalte1, spalte2, ...
  INTO variable1, variable2, ...
  FROM deine_tabelle
 WHERE deine_bedingung; -- Diese Bedingung sollte sicherstellen, dass nur EINE Zeile zurückkommt!
```

* Die Anzahl und die Datentypen der Spalten in der `SELECT`-Liste müssen genau zur Anzahl und den Datentypen der Variablen in der `INTO`-Klausel passen.
* **Wichtig:** Die Verwendung von `%TYPE` für die Variablen wird hier dringend empfohlen, um Typ-Inkonsistenzen zu vermeiden.

**Was passiert, wenn nicht genau eine Zeile zurückkommt?**

* **Keine Zeile gefunden:** Wenn die `WHERE`-Klausel keine Zeile findet, löst Oracle die vordefinierte Exception `NO_DATA_FOUND` aus.
* **Mehr als eine Zeile gefunden:** Wenn die `WHERE`-Klausel mehr als eine Zeile zurückliefert, löst Oracle die vordefinierte Exception `TOO_MANY_ROWS` aus.

**Grundlegende Fehlerbehandlung mit dem `EXCEPTION`-Block**

Um auf diese (und andere) Fehler kontrolliert zu reagieren und einen Programmabbruch zu verhindern, verwendest du den `EXCEPTION`-Block am Ende deines PL/SQL-Blocks.

```sql
EXCEPTION
  WHEN NO_DATA_FOUND THEN
    -- Code, der ausgeführt wird, wenn keine Zeile gefunden wurde
    DBMS_OUTPUT.PUT_LINE('Fehler: Keine Daten gefunden!');

  WHEN TOO_MANY_ROWS THEN
    -- Code, der ausgeführt wird, wenn zu viele Zeilen gefunden wurden
    DBMS_OUTPUT.PUT_LINE('Fehler: Zu viele Zeilen zurückgegeben!');

  WHEN OTHERS THEN
    -- Code für alle anderen, unerwarteten Fehler
    -- SQLCODE und SQLERRM sind spezielle Funktionen, die Fehlercode und -meldung liefern
    DBMS_OUTPUT.PUT_LINE('Ein unerwarteter Fehler ist aufgetreten: ' || SQLCODE || ' - ' || SQLERRM);
```

**Beispiel: Mitarbeiterdaten abfragen**

Für dieses Beispiel nehmen wir an, es gibt eine Tabelle `MITARBEITER` mit den Spalten `MNR` (Mitarbeiternummer, Primärschlüssel, Typ NUMBER), `NACHNAME` (Typ VARCHAR2) und `GEHALT` (Typ NUMBER). *Wenn du eine andere Tabelle oder Spaltennamen hast, musst du das Beispiel entsprechend anpassen.*

```sql
-- Serveroutput einschalten
SET SERVEROUTPUT ON;

DECLARE
  -- Variablen deklarieren, idealerweise mit %TYPE passend zu den Tabellenspalten
  v_mnr         mitarbeiter.mnr%TYPE       := 7788; -- Mitarbeiter-Nummer, die wir suchen
  v_nachname    mitarbeiter.nachname%TYPE;
  v_gehalt      mitarbeiter.gehalt%TYPE;
  v_job         mitarbeiter.job%TYPE; -- Nehmen wir an, es gibt auch eine JOB-Spalte

BEGIN
  -- Versuche, die Daten für den Mitarbeiter mit der gegebenen MNR zu holen
  DBMS_OUTPUT.PUT_LINE('Suche Mitarbeiter mit MNR: ' || v_mnr);

  SELECT nachname, gehalt, job -- Spalten, die wir lesen wollen
    INTO v_nachname, v_gehalt, v_job -- Variablen, in die gespeichert wird (Reihenfolge beachten!)
    FROM mitarbeiter               -- Tabelle, aus der wir lesen
   WHERE mnr = v_mnr;              -- Bedingung, die genau eine Zeile liefern sollte (da MNR Primärschlüssel ist)

  -- Wenn die SELECT INTO Anweisung erfolgreich war (genau eine Zeile gefunden),
  -- wird der Code hier fortgesetzt:
  DBMS_OUTPUT.PUT_LINE('Mitarbeiter gefunden:');
  DBMS_OUTPUT.PUT_LINE('  Nachname: ' || v_nachname);
  DBMS_OUTPUT.PUT_LINE('  Gehalt:   ' || v_gehalt);
  DBMS_OUTPUT.PUT_LINE('  Job:      ' || v_job);

  -- Beispiel für eine DML-Anweisung (Datenmanipulation): Gehalt erhöhen
  UPDATE mitarbeiter
     SET gehalt = gehalt * 1.05 -- Gehalt um 5% erhöhen
   WHERE mnr = v_mnr;

  DBMS_OUTPUT.PUT_LINE('Gehalt für ' || v_nachname || ' wurde um 5% erhöht.');
  -- Wichtig: Änderungen durch INSERT/UPDATE/DELETE sind standardmäßig Teil der aktuellen
  -- Transaktion. Sie müssen mit COMMIT permanent gemacht oder mit ROLLBACK verworfen werden.
  -- In einfachen anonymen Blöcken wird dies oft am Ende implizit durch das SQL-Tool gehandhabt,
  -- aber in Prozeduren etc. muss man COMMIT/ROLLBACK explizit steuern! Wir kommen später darauf zurück.
  -- COMMIT; -- würde die Änderung dauerhaft speichern
  -- ROLLBACK; -- würde die Änderung verwerfen

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    -- Dieser Block wird ausgeführt, wenn die SELECT INTO Anweisung keine Zeile fand
    DBMS_OUTPUT.PUT_LINE('FEHLER: Mitarbeiter mit MNR ' || v_mnr || ' nicht gefunden!');

  WHEN TOO_MANY_ROWS THEN
    -- Dieser Block wird ausgeführt, wenn die SELECT INTO Anweisung mehr als eine Zeile fand
    -- (Sollte bei Suche nach Primärschlüssel nicht passieren)
    DBMS_OUTPUT.PUT_LINE('FEHLER: Mehr als ein Mitarbeiter mit MNR ' || v_mnr || ' gefunden!');

  WHEN OTHERS THEN
    -- Dieser Block fängt alle anderen möglichen Fehler ab
    DBMS_OUTPUT.PUT_LINE('Ein unerwarteter Fehler ist aufgetreten!');
    DBMS_OUTPUT.PUT_LINE('Fehlercode: ' || SQLCODE); -- Gibt den Oracle-Fehlercode aus (z.B. -942 für Tabelle nicht gefunden)
    DBMS_OUTPUT.PUT_LINE('Fehlermeldung: ' || SQLERRM); -- Gibt die Oracle-Fehlermeldung aus

END;
/
```

**Zusammenfassung Schritt 3:**

* Du kannst SQL-Anweisungen (`SELECT`, `INSERT`, `UPDATE`, `DELETE`...) direkt im `BEGIN`-Teil von PL/SQL verwenden.
* `SELECT INTO` wird verwendet, um das Ergebnis einer Abfrage, die **genau eine Zeile** liefert, in PL/SQL-Variablen zu speichern.
* Du kennst die potenziellen Exceptions `NO_DATA_FOUND` und `TOO_MANY_ROWS` bei `SELECT INTO`.
* Du hast den `EXCEPTION`-Block kennengelernt, um auf diese und andere Fehler (`OTHERS`) kontrolliert zu reagieren.
* Du hast gesehen, wie man Variablen (idealerweise mit `%TYPE` deklariert) in der `INTO`-Klausel und in der `WHERE`-Klausel verwendet.

---

> Prima, machen wir weiter mit Ablaufsteuerung.

# **Schritt 4: Ablaufsteuerung mit Bedingungen (IF und CASE)**

Bisher lief unser PL/SQL-Code einfach von oben nach unten ab (abgesehen von Sprüngen in den `EXCEPTION`-Block). Mit `IF`- und `CASE`-Anweisungen können wir den Programmfluss basierend auf Bedingungen steuern, also entscheiden, welcher Code unter welchen Umständen ausgeführt wird.

**Die `IF`-Anweisung**

Die `IF`-Anweisung prüft eine Bedingung. Wenn die Bedingung `TRUE` ist, wird der Codeblock nach `THEN` ausgeführt. Optional kannst du mit `ELSE` einen Codeblock angeben, der ausgeführt wird, wenn die Bedingung `FALSE` (oder `NULL`) ist. Mit `ELSIF` kannst du weitere Bedingungen prüfen, falls die vorherige(n) `FALSE` waren.

**Syntax-Varianten:**

1.  **Einfaches IF:**
    ```sql
    IF bedingung THEN
      -- Code, der ausgeführt wird, wenn bedingung TRUE ist
    END IF;
    ```

2.  **IF THEN ELSE:**
    ```sql
    IF bedingung THEN
      -- Code, wenn bedingung TRUE ist
    ELSE
      -- Code, wenn bedingung FALSE oder NULL ist
    END IF;
    ```

3.  **IF THEN ELSIF ELSE:**
    ```sql
    IF bedingung1 THEN
      -- Code, wenn bedingung1 TRUE ist
    ELSIF bedingung2 THEN
      -- Code, wenn bedingung1 FALSE/NULL und bedingung2 TRUE ist
    ELSIF bedingung3 THEN
      -- Code, wenn bedingung1/2 FALSE/NULL und bedingung3 TRUE ist
    ELSE -- Optional
      -- Code, wenn alle vorherigen Bedingungen FALSE oder NULL sind
    END IF; -- Wichtig: Nur ein END IF am Schluss!
    ```

**Bedingungen:**
Bedingungen sind Ausdrücke, die `TRUE`, `FALSE` oder `NULL` ergeben. Typische Operatoren sind:
* Vergleich: `=`, `>`, `<`, `>=`, `<=`, `<>` (oder `!=`)
* Logisch: `AND`, `OR`, `NOT`
* Spezial: `IS NULL`, `IS NOT NULL`, `BETWEEN x AND y`, `IN (wert1, wert2, ...)`, `LIKE 'muster'`

**Wichtig bei `NULL`:** Ein Vergleich mit `NULL` (z.B. `variable = NULL`) ergibt immer `NULL`, nicht `TRUE` oder `FALSE`. Verwende stattdessen `variable IS NULL` oder `variable IS NOT NULL`. In `IF`-Bedingungen wird `NULL` wie `FALSE` behandelt, d.h. der `THEN`-Zweig wird nicht betreten.

**Die `CASE`-Anweisung**

`CASE` ist oft eine lesbarere Alternative zu verschachtelten `IF`-Anweisungen, besonders wenn man eine Variable oder einen Ausdruck mit mehreren möglichen Werten vergleicht. Es gibt zwei Formen:

1.  **CASE-Anweisung (Statement):** Steuert den Ablauf, ähnlich wie `IF`.
    ```sql
    CASE ausdruck -- oder keine Angabe für "searched case"
      WHEN wert1 THEN
        -- Code für wert1
      WHEN wert2 THEN
        -- Code für wert2
      WHEN wert_n THEN
        -- Code für wert_n
      ELSE -- Optional
        -- Code, wenn kein vorheriges WHEN zutraf
    END CASE; -- Wichtig: Endet mit END CASE;
    ```
    Man kann auch einen "searched case" ohne `ausdruck` am Anfang schreiben, wobei jedes `WHEN` eine eigene Bedingung enthält:
    ```sql
    CASE
      WHEN bedingung1 THEN
        -- Code für bedingung1
      WHEN bedingung2 THEN
        -- Code für bedingung2
      ELSE
        -- Code, wenn keine Bedingung zutraf
    END CASE;
    ```

2.  **CASE-Ausdruck (Expression):** Wird *innerhalb* einer Anweisung (z.B. einer Zuweisung oder `SELECT`-Liste) verwendet, um einen *Wert* basierend auf Bedingungen zurückzugeben.
    ```sql
    variable := CASE ausdruck
                  WHEN wert1 THEN ergebnis1
                  WHEN wert2 THEN ergebnis2
                  ELSE standard_ergebnis
                END; -- Wichtig: Endet nur mit END;
    ```
    Auch hier gibt es die "searched" Variante:
    ```sql
    variable := CASE
                  WHEN bedingung1 THEN ergebnis1
                  WHEN bedingung2 THEN ergebnis2
                  ELSE standard_ergebnis
                END;
    ```

**Beispiel: Mitarbeiter analysieren**

Wir holen wieder Daten eines Mitarbeiters und wenden dann `IF` und `CASE` an. Wir nehmen wieder die `MITARBEITER`-Tabelle an (mit `MNR`, `NACHNAME`, `GEHALT`, `JOB`, `ABTNR` (Abteilungsnummer)).

```sql
-- Serveroutput einschalten
SET SERVEROUTPUT ON;

DECLARE
  -- Variablen für Mitarbeiterdaten
  v_mnr         mitarbeiter.mnr%TYPE       := 7839; -- KING
  v_nachname    mitarbeiter.nachname%TYPE;
  v_gehalt      mitarbeiter.gehalt%TYPE;
  v_job         mitarbeiter.job%TYPE;
  v_abtnr       mitarbeiter.abtnr%TYPE;

  -- Variable für Analyse-Ergebnis
  v_gehaltsklasse VARCHAR2(20);
  v_bonus_faktor  NUMBER := 0; -- Standardmäßig kein Bonus
  v_abteilung_txt VARCHAR2(50);

BEGIN
  -- Mitarbeiterdaten holen
  SELECT nachname, gehalt, job, abtnr
    INTO v_nachname, v_gehalt, v_job, v_abtnr
    FROM mitarbeiter
   WHERE mnr = v_mnr;

  DBMS_OUTPUT.PUT_LINE('Analysiere Mitarbeiter: ' || v_nachname || ' (MNR: ' || v_mnr || ')');
  DBMS_OUTPUT.PUT_LINE('Job: ' || v_job || ', Gehalt: ' || v_gehalt || ', Abteilung: ' || v_abtnr);

  -- *** IF-Beispiele ***

  -- 1. Gehaltsklasse bestimmen mit IF/ELSIF/ELSE
  IF v_gehalt >= 5000 THEN
    v_gehaltsklasse := 'Hoch';
  ELSIF v_gehalt >= 2500 THEN
    v_gehaltsklasse := 'Mittel';
  ELSE
    v_gehaltsklasse := 'Niedrig';
  END IF; -- Ende des IF-Blocks

  DBMS_OUTPUT.PUT_LINE('Gehaltsklasse (IF): ' || v_gehaltsklasse);

  -- 2. Bonus nur für bestimmte Jobs und nur wenn Gehalt unter 4000
  IF v_job IN ('MANAGER', 'PRESIDENT') AND v_gehalt < 4000 THEN
    v_bonus_faktor := 0.1; -- 10% Bonus
    DBMS_OUTPUT.PUT_LINE('Qualifiziert sich für 10% Bonus.');
  ELSIF v_job = 'SALESMAN' THEN
     v_bonus_faktor := 0.05; -- 5% Bonus
     DBMS_OUTPUT.PUT_LINE('Qualifiziert sich für 5% Bonus.');
  ELSE
     DBMS_OUTPUT.PUT_LINE('Kein Standardbonus für diesen Job.');
     -- v_bonus_faktor bleibt 0 (wie initialisiert)
  END IF;


  -- *** CASE-Beispiele ***

  -- 1. Abteilungsnamen bestimmen mit CASE-Anweisung (Statement)
  CASE v_abtnr
    WHEN 10 THEN
      v_abteilung_txt := 'Buchhaltung (ACCOUNTING)';
    WHEN 20 THEN
      v_abteilung_txt := 'Forschung (RESEARCH)';
    WHEN 30 THEN
      v_abteilung_txt := 'Verkauf (SALES)';
    ELSE
      v_abteilung_txt := 'Unbekannte Abteilung (' || v_abtnr || ')';
  END CASE; -- Ende der CASE-Anweisung

  DBMS_OUTPUT.PUT_LINE('Abteilung (CASE): ' || v_abteilung_txt);

  -- 2. Bonusfaktor überschreiben mit CASE-Ausdruck (Expression) basierend auf Job
  --    (Nur zur Demo, überschreibt ggf. den Wert aus dem IF oben)
  v_bonus_faktor := CASE v_job
                      WHEN 'PRESIDENT' THEN 0.20 -- 20%
                      WHEN 'MANAGER'   THEN 0.15 -- 15%
                      WHEN 'SALESMAN'  THEN 0.12 -- 12% (anderer Wert als im IF)
                      ELSE 0.05                -- 5% für alle anderen
                    END; -- Ende des CASE-Ausdrucks

  DBMS_OUTPUT.PUT_LINE('Bonusfaktor (CASE Expression): ' || v_bonus_faktor);

  -- 3. Searched CASE Statement zur Überprüfung komplexerer Bedingungen
  CASE
    WHEN v_job = 'PRESIDENT' THEN
      DBMS_OUTPUT.PUT_LINE('Dies ist der Chef!');
    WHEN v_gehalt > 3000 AND v_abtnr = 20 THEN
       DBMS_OUTPUT.PUT_LINE('Gut bezahlter Mitarbeiter in der Forschung.');
    ELSE
       DBMS_OUTPUT.PUT_LINE('Mitarbeiter erfüllt keine speziellen Kriterien.');
  END CASE;


EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('FEHLER: Mitarbeiter mit MNR ' || v_mnr || ' nicht gefunden!');
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Unerwarteter Fehler: ' || SQLCODE || ' - ' || SQLERRM);

END;
/
```

**Zusammenfassung Schritt 4:**

* Du kannst mit `IF`, `ELSIF` und `ELSE` Codeblöcke basierend auf Bedingungen ausführen.
* Du kennst die verschiedenen Vergleichs- und Logikoperatoren für Bedingungen.
* Du weißt, wie man mit `NULL` in Bedingungen umgeht (`IS NULL`, `IS NOT NULL`).
* Du kannst die `CASE`-Anweisung (Statement) für die Ablaufsteuerung verwenden, oft als lesbarere Alternative zu `IF/ELSIF`.
* Du kannst den `CASE`-Ausdruck (Expression) verwenden, um einen Wert basierend auf Bedingungen zu ermitteln und zuzuweisen.
* Du kennst den Unterschied zwischen der einfachen und der "searched" Variante von `CASE`.

---

> Okay, lass uns nun lernen, wie man Codeblöcke wiederholt ausführt.

# **Schritt 5: Wiederholungen mit Schleifen (Loops)**

Schleifen sind Kontrollstrukturen, die es ermöglichen, einen Block von Anweisungen mehrfach auszuführen. PL/SQL bietet drei Arten von Schleifen:

1.  **Einfache Schleife (`LOOP ... END LOOP`)**
2.  **`WHILE`-Schleife (`WHILE ... LOOP ... END LOOP`)**
3.  **`FOR`-Schleife (`FOR ... LOOP ... END LOOP`)**

**1. Einfache Schleife (`LOOP ... END LOOP`)**

Dies ist die grundlegendste Schleifenform. Der Code zwischen `LOOP` und `END LOOP` wird wiederholt ausgeführt, bis die Schleife explizit mit einer `EXIT`-Anweisung verlassen wird.

**Syntax:**

```sql
LOOP
  -- Anweisungen, die wiederholt werden
  IF bedingung THEN
    EXIT; -- Verlässt die Schleife sofort
  END IF;
  -- oder häufiger:
  EXIT WHEN bedingung; -- Verlässt die Schleife, wenn die Bedingung TRUE ist

  -- Optional: CONTINUE [WHEN bedingung];
  -- CONTINUE überspringt den Rest der aktuellen Iteration und startet die nächste.
END LOOP;
```

* **Achtung:** Ohne eine `EXIT`-Bedingung entsteht eine Endlosschleife! `EXIT WHEN` ist die gebräuchlichste Form, um eine einfache Schleife zu beenden.

**Beispiel (Einfache Schleife):**

```sql
SET SERVEROUTPUT ON;

DECLARE
  v_zaehler NUMBER := 0;
  v_limit   NUMBER := 5;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Einfache LOOP ---');
  LOOP
    v_zaehler := v_zaehler + 1; -- Zähler erhöhen

    -- Beispiel für CONTINUE: Überspringe gerade Zahlen
    CONTINUE WHEN MOD(v_zaehler, 2) = 0; -- MOD gibt den Rest einer Division zurück

    DBMS_OUTPUT.PUT_LINE('Aktueller Zähler (ungerade): ' || v_zaehler);

    -- Abbruchbedingung: Verlasse die Schleife, wenn Zähler >= Limit
    EXIT WHEN v_zaehler >= v_limit;

    -- Dieser Teil wird nur erreicht, wenn weder CONTINUE noch EXIT ausgelöst wurde
    DBMS_OUTPUT.PUT_LINE('   (Iteration abgeschlossen)');

  END LOOP;
  DBMS_OUTPUT.PUT_LINE('Schleife beendet. End-Zähler: ' || v_zaehler);
END;
/
```

**2. `WHILE`-Schleife (`WHILE ... LOOP ... END LOOP`)**

Diese Schleife führt den Codeblock aus, *solange* eine Bedingung am Anfang der Schleife `TRUE` ist. Die Bedingung wird *vor* jeder Iteration geprüft.

**Syntax:**

```sql
WHILE bedingung LOOP
  -- Anweisungen, die ausgeführt werden, solange bedingung TRUE ist
END LOOP;
```

* Wenn die `bedingung` schon beim ersten Mal `FALSE` (oder `NULL`) ist, wird der Schleifenkörper gar nicht erst betreten.
* Innerhalb der Schleife muss in der Regel etwas passieren, das die Bedingung irgendwann `FALSE` werden lässt, um eine Endlosschleife zu vermeiden (z.B. ein Zähler verändert sich).

**Beispiel (`WHILE`-Schleife):**

```sql
SET SERVEROUTPUT ON;

DECLARE
  v_guthaben NUMBER := 100;
  v_abbuchung NUMBER := 25;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- WHILE LOOP ---');
  DBMS_OUTPUT.PUT_LINE('Startguthaben: ' || v_guthaben);

  WHILE v_guthaben > 0 LOOP
    DBMS_OUTPUT.PUT_LINE(' Buche ab: ' || v_abbuchung);
    v_guthaben := v_guthaben - v_abbuchung;
    DBMS_OUTPUT.PUT_LINE(' Restguthaben: ' || v_guthaben);

    -- Sicherheits-Exit (falls die Logik fehlerhaft wäre)
    IF v_guthaben < -1000 THEN
       DBMS_OUTPUT.PUT_LINE(' Sicherheitsabbruch!');
       EXIT;
    END IF;

  END LOOP;
  DBMS_OUTPUT.PUT_LINE('Schleife beendet. Endguthaben: ' || v_guthaben);
END;
/
```

**3. `FOR`-Schleife (`FOR ... LOOP ... END LOOP`)**

Diese Schleife wird verwendet, um einen Codeblock für eine feste Anzahl von Wiederholungen auszuführen. PL/SQL bietet zwei Varianten:

* **Numerische `FOR`-Schleife:** Iteriert über einen Bereich von ganzen Zahlen.
* **Cursor `FOR`-Schleife:** Iteriert über die Zeilen eines Abfrageergebnisses (diese sehen wir uns später genauer an, wenn wir Cursor behandeln).

**Syntax (Numerische `FOR`-Schleife):**

```sql
FOR schleifenzaehler IN [REVERSE] untergrenze .. obergrenze LOOP
  -- Anweisungen
  -- Der schleifenzaehler ist hier verfügbar
END LOOP;
```

* `schleifenzaehler`: Eine implizit deklarierte Integer-Variable. Du musst sie *nicht* im `DECLARE`-Teil deklarieren. Sie ist nur innerhalb der Schleife gültig. Ihr Wert kann innerhalb der Schleife nicht verändert werden (du kannst ihn aber lesen).
* `untergrenze .. obergrenze`: Der Bereich der ganzen Zahlen, der durchlaufen wird (inklusive Grenzen).
* `REVERSE` (optional): Kehrt die Reihenfolge um, d.h. es wird von `obergrenze` abwärts bis `untergrenze` gezählt.

**Beispiel (Numerische `FOR`-Schleife):**

```sql
SET SERVEROUTPUT ON;

BEGIN
  DBMS_OUTPUT.PUT_LINE('--- FOR LOOP (Aufsteigend) ---');
  FOR i IN 1 .. 5 LOOP -- i wird implizit deklariert und zählt von 1 bis 5
    DBMS_OUTPUT.PUT_LINE('Iteration ' || i);
    -- Versuch, den Zähler zu ändern (führt zu Kompilierfehler):
    -- i := i + 1;
  END LOOP;

  DBMS_OUTPUT.PUT_LINE('--- FOR LOOP (Absteigend mit REVERSE) ---');
  FOR k IN REVERSE 1 .. 3 LOOP -- k zählt 3, 2, 1
     DBMS_OUTPUT.PUT_LINE('Countdown: ' || k);
  END LOOP;

  -- Beispiel: Summe berechnen
  DECLARE
    v_summe NUMBER := 0;
  BEGIN
    FOR j IN 1 .. 10 LOOP
      v_summe := v_summe + j;
    END LOOP;
    DBMS_OUTPUT.PUT_LINE('Summe von 1 bis 10: ' || v_summe);
  END;

END;
/
```

**Zusammenfassung Schritt 5:**

* Du kennst die drei Schleifentypen: Einfache `LOOP`, `WHILE LOOP` und `FOR LOOP`.
* Du weißt, wie man Schleifen mit `EXIT` oder `EXIT WHEN` verlässt und wie `CONTINUE` funktioniert.
* Du verstehst, dass die `WHILE`-Schleife die Bedingung am Anfang prüft.
* Du kannst die numerische `FOR`-Schleife verwenden, um einen Codeblock für einen festen Zahlenbereich auszuführen (auf- oder absteigend mit `REVERSE`).
* Du weißt, dass der Zähler der `FOR`-Schleife implizit deklariert wird und nur innerhalb der Schleife gültig ist.

---

# **Schritt 6: Umgang mit mehreren Zeilen (Cursor)**

Ein Cursor ist im Grunde ein Zeiger auf einen Speicherbereich im Datenbankserver, der das Ergebnis einer SQL-Abfrage (die Ergebnismenge oder "Result Set") aufnimmt und es dir ermöglicht, diese Zeilen einzeln, also Zeile für Zeile, in deinem PL/SQL-Code zu verarbeiten.

Es gibt zwei Hauptarten, mit Cursorn zu arbeiten:

1.  **Explizite Cursor:** Du deklarierst und steuerst den Cursor manuell. Das gibt dir volle Kontrolle, ist aber auch aufwändiger.
2.  **Cursor `FOR` Loop:** Eine viel einfachere und oft bevorzugte Syntax, die viele der manuellen Schritte automatisiert.

**1. Explizite Cursor**

Die Arbeit mit expliziten Cursorn folgt immer diesen Schritten:

* **DECLARE:** Deklariere den Cursor im `DECLARE`-Teil. Du gibst ihm einen Namen und verknüpfst ihn mit einer `SELECT`-Anweisung.
    ```sql
    CURSOR cursor_name IS SELECT spalte1, spalte2, ... FROM ... WHERE ...;
    ```
* **OPEN:** Öffne den Cursor im `BEGIN`-Teil. Dadurch wird die Abfrage ausgeführt und die Ergebnismenge im Speicherbereich bereitgestellt.
    ```sql
    OPEN cursor_name;
    ```
* **FETCH:** Hole die *nächste* verfügbare Zeile aus der Ergebnismenge und speichere die Spaltenwerte in PL/SQL-Variablen.
    ```sql
    FETCH cursor_name INTO variable1, variable2, ...;
    ```
    Die Variablen müssen in Anzahl und Typ zu den Spalten in der `SELECT`-Liste des Cursors passen (`%TYPE` ist hier wieder sehr nützlich!).
* **Prüfen (Cursor-Attribute):** Nach jedem `Workspace` musst du prüfen, ob überhaupt eine Zeile geholt wurde. Dafür gibt es Cursor-Attribute:
    * `cursor_name%FOUND`: Ist `TRUE`, wenn der letzte `Workspace` erfolgreich eine Zeile geholt hat.
    * `cursor_name%NOTFOUND`: Ist `TRUE`, wenn der letzte `Workspace` *keine* Zeile mehr holen konnte (d.h., das Ende der Ergebnismenge ist erreicht). Dies ist das häufigste Kriterium, um eine Schleife zu beenden.
    * `cursor_name%ROWCOUNT`: Gibt die Anzahl der Zeilen zurück, die *bisher* mit diesem Cursor geholt wurden.
    * `cursor_name%ISOPEN`: Ist `TRUE`, wenn der Cursor geöffnet ist.
* **CLOSE:** Schließe den Cursor, wenn du fertig bist. Das gibt die Ressourcen (Speicher etc.) wieder frei. Es ist sehr wichtig, geöffnete Cursor auch wieder zu schließen!
    ```sql
    CLOSE cursor_name;
    ```

**Typischer Ablauf mit explizitem Cursor:**

```sql
DECLARE
  -- Variablen zum Speichern der Daten einer Zeile
  v_spalte1 tabelle.spalte1%TYPE;
  v_spalte2 tabelle.spalte2%TYPE;

  -- Cursor deklarieren
  CURSOR c_mein_cursor IS
    SELECT spalte1, spalte2 FROM tabelle WHERE bedingung;
BEGIN
  OPEN c_mein_cursor; -- Abfrage ausführen

  LOOP
    FETCH c_mein_cursor INTO v_spalte1, v_spalte2; -- Nächste Zeile holen

    -- Schleife verlassen, wenn keine Zeile mehr gefunden wurde
    EXIT WHEN c_mein_cursor%NOTFOUND;

    -- Hier die Daten der aktuellen Zeile verarbeiten
    DBMS_OUTPUT.PUT_LINE('Gefunden: ' || v_spalte1 || ', ' || v_spalte2);

  END LOOP;

  CLOSE c_mein_cursor; -- Wichtig: Cursor schließen!
END;
/
```

**Beispiel (Expliziter Cursor für Mitarbeiter einer Abteilung):**
Wir listen Namen und Gehälter aller Mitarbeiter der Abteilung 20 auf (angenommen, es gibt `MITARBEITER` mit `NACHNAME`, `GEHALT`, `ABTNR`).

```sql
SET SERVEROUTPUT ON;

DECLARE
  v_nachname mitarbeiter.nachname%TYPE;
  v_gehalt   mitarbeiter.gehalt%TYPE;

  -- Cursor deklarieren, der Mitarbeiter aus Abteilung 20 holt
  CURSOR c_abt_20 IS
    SELECT nachname, gehalt
      FROM mitarbeiter
     WHERE abtnr = 20
     ORDER BY nachname; -- Sortierung ist optional, aber oft sinnvoll
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Expliziter Cursor für Abteilung 20 ---');
  OPEN c_abt_20; -- Cursor öffnen

  LOOP
    FETCH c_abt_20 INTO v_nachname, v_gehalt; -- Nächste Zeile holen

    EXIT WHEN c_abt_20%NOTFOUND; -- Schleife verlassen, wenn keine Daten mehr da sind

    -- Zeile verarbeiten
    DBMS_OUTPUT.PUT_LINE('  Mitarbeiter: ' || v_nachname || ', Gehalt: ' || v_gehalt);
    DBMS_OUTPUT.PUT_LINE('  (Bisher geholt: ' || c_abt_20%ROWCOUNT || ')'); -- Zeigt %ROWCOUNT

  END LOOP;

  CLOSE c_abt_20; -- Cursor schließen

  -- Prüfung nach der Schleife (optional)
  IF c_abt_20%ROWCOUNT = 0 THEN
    DBMS_OUTPUT.PUT_LINE('Keine Mitarbeiter in Abteilung 20 gefunden.');
  ELSE
     DBMS_OUTPUT.PUT_LINE('Verarbeitung abgeschlossen. Gesamt: ' || c_abt_20%ROWCOUNT || ' Mitarbeiter.');
  END IF;

  -- DBMS_OUTPUT.PUT_LINE(c_abt_20%ISOPEN); -- Würde FALSE ausgeben (nach CLOSE)

EXCEPTION
  WHEN OTHERS THEN
    -- Sicherstellen, dass der Cursor auch im Fehlerfall geschlossen wird
    IF c_abt_20%ISOPEN THEN
      CLOSE c_abt_20;
    END IF;
    DBMS_OUTPUT.PUT_LINE('Fehler: ' || SQLCODE || ' - ' || SQLERRM);
END;
/
```

**2. Cursor `FOR` Loop**

Dies ist die **bevorzugte Methode** für die meisten Fälle, da sie viel kürzer, lesbarer und weniger fehleranfällig ist. PL/SQL kümmert sich automatisch um `OPEN`, `Workspace`, das Prüfen auf `%NOTFOUND` und `CLOSE`.

**Syntax:**

```sql
-- Variante 1: Mit explizit deklariertem Cursor
DECLARE
  CURSOR cursor_name IS SELECT ...;
BEGIN
  FOR record_variable IN cursor_name LOOP
    -- Anweisungen
    -- Zugriff auf Spalten über: record_variable.spaltenname
  END LOOP;
  -- Kein OPEN, FETCH, CLOSE nötig!
END;

-- Variante 2: Mit implizitem Cursor (SELECT direkt in der Schleife)
BEGIN
  FOR record_variable IN (SELECT spalte1, spalte2 ... FROM ... WHERE ...) LOOP
    -- Anweisungen
    -- Zugriff auf Spalten über: record_variable.spaltenname
  END LOOP;
  -- Kein DECLARE, OPEN, FETCH, CLOSE nötig!
END;
```

* `record_variable`: Eine **implizit deklarierte** Variable vom Typ `RECORD`. Du musst sie *nicht* im `DECLARE`-Teil deklarieren. Ihre Struktur (Felder) entspricht den Spalten der `SELECT`-Liste des Cursors. Sie ist nur innerhalb der Schleife gültig.

**Beispiel (Cursor `FOR` Loop für Mitarbeiter einer Abteilung):**

```sql
SET SERVEROUTPUT ON;

-- Beispiel mit implizitem Cursor (SELECT direkt in der Schleife)
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Cursor FOR Loop (implizit) für Abteilung 30 ---');
  DECLARE
     v_anzahl_ma NUMBER := 0;
  BEGIN
     FOR ma_zeile IN (SELECT nachname, job, gehalt FROM mitarbeiter WHERE abtnr = 30 ORDER BY nachname)
     LOOP
       -- ma_zeile ist ein implizit deklarierter Record
       -- Zugriff auf die Spaltenwerte über ma_zeile.spaltenname
       DBMS_OUTPUT.PUT_LINE('  MA: ' || ma_zeile.nachname ||
                            ', Job: ' || ma_zeile.job ||
                            ', Gehalt: ' || ma_zeile.gehalt);
       v_anzahl_ma := v_anzahl_ma + 1; -- oder SQL%ROWCOUNT nach der Schleife
     END LOOP; -- Automatisches OPEN, FETCH, CLOSE hier!

     DBMS_OUTPUT.PUT_LINE('Anzahl Mitarbeiter in Abt 30 gefunden: ' || v_anzahl_ma);
     -- Alternativ: v_anzahl_ma := SQL%ROWCOUNT; -- SQL%ROWCOUNT gibt die Anzahl der von der letzten FOR-Loop verarbeiteten Zeilen an.
     -- DBMS_OUTPUT.PUT_LINE('Anzahl Mitarbeiter in Abt 30 gefunden (SQL%ROWCOUNT): ' || SQL%ROWCOUNT);

  END; -- Innerer Block nur für v_anzahl_ma
END;
/

-- Beispiel mit explizit deklariertem Cursor
DECLARE
  -- Cursor explizit deklarieren
  CURSOR c_alle_manager IS
    SELECT nachname, gehalt FROM mitarbeiter WHERE job = 'MANAGER' ORDER BY gehalt DESC;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Cursor FOR Loop (explizit) für Manager ---');

  FOR manager_rec IN c_alle_manager -- Verwende den deklarierten Cursor
  LOOP
    -- manager_rec wird implizit deklariert
    DBMS_OUTPUT.PUT_LINE('  Manager: ' || manager_rec.nachname ||
                         ', Gehalt: ' || manager_rec.gehalt);
  END LOOP; -- Automatisches OPEN, FETCH, CLOSE hier!

END;
/
```

**Zusammenfassung Schritt 6:**

* Du weißt jetzt, warum Cursor notwendig sind (Verarbeitung von Abfragen mit mehreren Ergebniszeilen).
* Du kennst die Schritte zur Verwendung expliziter Cursor: `DECLARE`, `OPEN`, `Workspace` (in einer Schleife), Prüfen mit `%FOUND` / `%NOTFOUND`, `CLOSE`.
* Du hast die Cursor-Attribute `%FOUND`, `%NOTFOUND`, `%ROWCOUNT`, `%ISOPEN` kennengelernt.
* Du kennst die Syntax und die Vorteile der Cursor `FOR` Loop (automatische Behandlung von `OPEN`, `Workspace`, `CLOSE` und implizite Record-Deklaration).
* Du kannst sowohl einen explizit deklarierten Cursor als auch eine `SELECT`-Anweisung direkt in einer Cursor `FOR` Loop verwenden.
* Die Cursor `FOR` Loop ist in den meisten Fällen die einfachere und sicherere Wahl.

 ---

# **Schritt 7: Wiederverwendbarer Code (Stored Procedures)**

Eine **Stored Procedure** (gespeicherte Prozedur) ist ein benannter PL/SQL-Block, der kompiliert und dauerhaft in der Oracle-Datenbank gespeichert wird (als Schema-Objekt). Einmal erstellt, kann sie von verschiedenen Benutzern oder Anwendungen immer wieder aufgerufen werden.

**Vorteile von Stored Procedures:**

* **Wiederverwendbarkeit:** Code muss nur einmal geschrieben und getestet werden.
* **Wartbarkeit:** Änderungen müssen nur an einer zentralen Stelle vorgenommen werden.
* **Modularität:** Komplexe Aufgaben können in logische Untereinheiten aufgeteilt werden.
* **Performance:** Der Code ist kompiliert und wird direkt in der Datenbank ausgeführt, was Netzwerklast reduzieren kann (statt den ganzen Block zu senden, sendet man nur den Aufruf).
* **Sicherheit:** Du kannst Benutzern das Recht geben, eine Prozedur auszuführen (`GRANT EXECUTE`), ohne ihnen Rechte auf die Tabellen geben zu müssen, die *innerhalb* der Prozedur verwendet werden.

**Erstellen einer Prozedur (`CREATE PROCEDURE`)**

Die grundlegende Syntax zum Erstellen einer Prozedur lautet:

```sql
CREATE [OR REPLACE] PROCEDURE prozedur_name
  [(parameter1 [modus] datentyp [DEFAULT wert],
    parameter2 [modus] datentyp [DEFAULT wert],
    ... )]
IS -- oder AS (sind synonym)
  -- Deklarationsteil (Variablen, Konstanten, Cursor, Typen etc.)
BEGIN
  -- Ausführungsteil (PL/SQL- und SQL-Anweisungen)
EXCEPTION
  -- Optional: Exception-Handler-Teil
END [prozedur_name]; -- Optional, aber gute Praxis, den Namen hier zu wiederholen
/
-- Das Slash führt das CREATE PROCEDURE Statement aus (in SQL*Plus/SQL Developer)
```

* `CREATE OR REPLACE`: Sehr nützlich während der Entwicklung. Wenn die Prozedur schon existiert, wird sie ersetzt, ansonsten neu erstellt. Ohne `OR REPLACE` würde ein Fehler auftreten, wenn die Prozedur bereits existiert.
* `prozedur_name`: Der eindeutige Name der Prozedur im Schema.
* **Parameter:** (Optional) Ermöglichen die Übergabe von Werten an die Prozedur und die Rückgabe von Werten aus der Prozedur.
    * `modus`: Bestimmt die Richtung des Parameters:
        * `IN` (Standard, wenn nichts angegeben): Der Wert wird *in* die Prozedur übergeben. Der Parameter verhält sich wie eine Konstante; sein Wert kann innerhalb der Prozedur nicht geändert werden.
        * `OUT`: Der Parameter wird verwendet, um einen Wert *aus* der Prozedur an den Aufrufer zurückzugeben. Innerhalb der Prozedur hat er anfangs keinen definierten Wert (ist `NULL`) und *muss* vor dem normalen Ende der Prozedur einen Wert zugewiesen bekommen.
        * `IN OUT`: Eine Kombination. Der Aufrufer übergibt einen Wert *in* die Prozedur, dieser Wert kann *innerhalb* der Prozedur geändert werden, und der (möglicherweise geänderte) Wert wird *an den Aufrufer zurückgegeben*.
    * `datentyp`: Der Datentyp des Parameters (z.B. `VARCHAR2`, `NUMBER`, `DATE`). `%TYPE` ist hier seltener, aber möglich.
    * `DEFAULT wert`: (Optional) Macht den Parameter optional. Wenn der Aufrufer keinen Wert für diesen Parameter angibt, wird der `DEFAULT`-Wert verwendet. Parameter mit `DEFAULT` sollten am Ende der Parameterliste stehen.
* `IS` oder `AS`: Leiten den Body der Prozedur ein.
* Der Rest (`DECLARE`, `BEGIN`, `EXCEPTION`, `END`) ist wie bei einem anonymen Block.

**Ausführen einer Prozedur**

Gespeicherte Prozeduren können auf verschiedene Weisen aufgerufen werden:

1.  **Von SQL\*Plus oder SQL Developer:**
    ```sql
    EXECUTE prozedur_name(argument1, argument2, ...);
    -- oder kurz:
    EXEC prozedur_name(argument1, argument2, ...);
    -- oder als anonymer Block:
    BEGIN
      prozedur_name(argument1, argument2, ...);
    END;
    /
    ```
2.  **Aus einem anderen PL/SQL-Block (Prozedur, Funktion, Trigger, anonymer Block):**
    ```sql
    BEGIN
      -- ... anderer Code ...
      prozedur_name(argument1, argument2, ...);
      -- ... weiterer Code ...
    END;
    /
    ```

**Parameterübergabe beim Aufruf:**

* **Positions-Notation:** Argumente werden in der Reihenfolge übergeben, in der die Parameter definiert wurden.
    `EXEC prozedur_name(10, 'Hallo', SYSDATE);`
* **Namens-Notation:** Argumente werden über `parameter_name => wert` übergeben. Die Reihenfolge spielt keine Rolle. Dies erhöht die Lesbarkeit, besonders bei vielen oder optionalen Parametern.
    `EXEC prozedur_name(param2 => 'Hallo', param1 => 10, param3 => SYSDATE);`
* **Gemischte Notation:** Erst Positions-, dann Namens-Notation ist erlaubt. Umgekehrt nicht.
    `EXEC prozedur_name(10, param3 => SYSDATE, param2 => 'Hallo');`

**Beispiele:**

**1. Einfache Prozedur (nur IN-Parameter, Default-Wert)**
Diese Prozedur gibt eine Nachricht mit einem optionalen Log-Level aus.

```sql
CREATE OR REPLACE PROCEDURE log_meldung (
  p_nachricht IN VARCHAR2,
  p_level     IN VARCHAR2 DEFAULT 'INFO' -- Default-Wert 'INFO'
)
IS
BEGIN
  -- SYSTIMESTAMP gibt Datum+Zeit+Sekundenbruchteile+Zeitzone zurück
  DBMS_OUTPUT.PUT_LINE(
    TO_CHAR(SYSTIMESTAMP, 'YYYY-MM-DD HH24:MI:SS.FF3 TZR') ||
    ' [' || UPPER(p_level) || '] ' || -- Wandelt Level in Großbuchstaben um
    p_nachricht
  );
END log_meldung;
/

-- Aufrufe:
SET SERVEROUTPUT ON;
BEGIN
  log_meldung('Systemstart erfolgt.'); -- p_level wird 'INFO' sein
  log_meldung(p_nachricht => 'Datenbankverbindung verloren!', p_level => 'ERROR'); -- Namensnotation
  log_meldung('Wartungsmodus aktiviert', 'WARN'); -- Positionsnotation
END;
/
```

**2. Prozedur mit IN- und OUT-Parametern**
Holt Details zu einem Mitarbeiter anhand der Mitarbeiternummer.

```sql
CREATE OR REPLACE PROCEDURE get_mitarbeiter_details (
  p_mnr       IN  mitarbeiter.mnr%TYPE,       -- Eingabe: Mitarbeiter-Nr
  p_nachname  OUT mitarbeiter.nachname%TYPE,  -- Ausgabe: Nachname
  p_gehalt    OUT mitarbeiter.gehalt%TYPE,    -- Ausgabe: Gehalt
  p_job       OUT mitarbeiter.job%TYPE        -- Ausgabe: Job
)
IS
BEGIN
  -- Daten mit SELECT INTO holen (erwartet genau eine Zeile)
  SELECT nachname, gehalt, job
    INTO p_nachname, p_gehalt, p_job
    FROM mitarbeiter
   WHERE mnr = p_mnr;

  -- Kein DBMS_OUTPUT hier, die Werte werden über die OUT-Parameter zurückgegeben

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    -- Im Fehlerfall OUT-Parameter auf NULL setzen (oder Fehler signalisieren)
    p_nachname := NULL;
    p_gehalt   := NULL;
    p_job      := NULL;
    -- Optional: Fehler weiterleiten oder loggen
    log_meldung('Mitarbeiter mit MNR ' || p_mnr || ' nicht gefunden.', 'WARN');
    -- RAISE; -- würde den NO_DATA_FOUND Fehler an den Aufrufer weitergeben

  WHEN OTHERS THEN
     log_meldung('Fehler in get_mitarbeiter_details für MNR ' || p_mnr || ': ' || SQLERRM, 'ERROR');
     -- Im unerwarteten Fehlerfall auch OUT-Parameter zurücksetzen
     p_nachname := NULL;
     p_gehalt   := NULL;
     p_job      := NULL;
     RAISE; -- Unerwarteten Fehler immer weitergeben!
END get_mitarbeiter_details;
/

-- Aufruf:
DECLARE
  v_name mitarbeiter.nachname%TYPE;
  v_sal  mitarbeiter.gehalt%TYPE;
  v_job  mitarbeiter.job%TYPE;
  v_gesuchte_mnr mitarbeiter.mnr%TYPE := 7788; -- SCOTT
BEGIN
  log_meldung('Rufe get_mitarbeiter_details für MNR ' || v_gesuchte_mnr || ' auf...');
  get_mitarbeiter_details(
      p_mnr      => v_gesuchte_mnr,
      p_nachname => v_name,   -- Variable für OUT-Parameter Nachname
      p_gehalt   => v_sal,    -- Variable für OUT-Parameter Gehalt
      p_job      => v_job     -- Variable für OUT-Parameter Job
  );

  -- Prüfen, ob Daten zurückkamen (wenn NO_DATA_FOUND intern behandelt wurde)
  IF v_name IS NOT NULL THEN
    log_meldung('Ergebnis: Name=' || v_name || ', Gehalt=' || v_sal || ', Job=' || v_job);
  ELSE
    log_meldung('Keine Details erhalten.');
  END IF;

  -- Beispiel für nicht existierende MNR
  v_gesuchte_mnr := 9999;
  log_meldung('Rufe get_mitarbeiter_details für MNR ' || v_gesuchte_mnr || ' auf...');
   get_mitarbeiter_details(v_gesuchte_mnr, v_name, v_sal, v_job); -- Positionsnotation
   IF v_name IS NOT NULL THEN
     log_meldung('Ergebnis: Name=' || v_name || ', Gehalt=' || v_sal || ', Job=' || v_job);
   ELSE
     log_meldung('Keine Details erhalten.');
   END IF;

END;
/
```

**3. Prozedur mit IN OUT Parameter**
Konvertiert einen übergebenen Namen in "Initcap"-Schreibweise (erster Buchstabe groß, Rest klein).

```sql
CREATE OR REPLACE PROCEDURE format_name_initcap (
  p_name IN OUT VARCHAR2 -- Nimmt Namen entgegen, gibt formatierten Namen zurück
)
IS
BEGIN
  IF p_name IS NOT NULL THEN
    -- INITCAP ist eine Standard-SQL-Funktion
    p_name := INITCAP(p_name);
  END IF;
END format_name_initcap;
/

-- Aufruf:
DECLARE
  v_test_name VARCHAR2(50) := 'max MUSTERMANN';
BEGIN
  log_meldung('Vorher: ' || v_test_name);
  format_name_initcap(v_test_name); -- Übergibt die Variable, ihr Wert wird geändert
  log_meldung('Nachher: ' || v_test_name);

  v_test_name := 'JOHN F. KENNEDY';
  log_meldung('Vorher: ' || v_test_name);
  format_name_initcap(p_name => v_test_name); -- Namensnotation
  log_meldung('Nachher: ' || v_test_name);
END;
/

```

**Wichtiger Hinweis zu Transaktionen (COMMIT/ROLLBACK):**
Wenn eine Prozedur DML-Anweisungen (`INSERT`, `UPDATE`, `DELETE`) enthält, werden diese Änderungen Teil der aufrufenden Transaktion. Die Prozedur selbst sollte normalerweise **kein** `COMMIT` oder `ROLLBACK` enthalten, es sei denn, sie soll eine eigenständige Transaktion darstellen (was seltener der Fall ist). Die Entscheidung über `COMMIT` (Änderungen dauerhaft speichern) oder `ROLLBACK` (Änderungen verwerfen) sollte beim Aufrufer liegen, der den gesamten Geschäftsvorgang überblickt.

**Zusammenfassung Schritt 7:**

* Du verstehst, was Stored Procedures sind und welche Vorteile sie bieten (Wiederverwendbarkeit, Wartbarkeit, Performance, Sicherheit, Modularität).
* Du kennst die `CREATE OR REPLACE PROCEDURE`-Syntax.
* Du verstehst die verschiedenen Parameter-Modi (`IN`, `OUT`, `IN OUT`) und wie man Default-Werte (`DEFAULT`) vergibt.
* Du weißt, wie man Prozeduren aufruft (`EXECUTE` oder direkter Aufruf) und wie man Parameter per Positions- oder Namens-Notation übergibt.
* Du hast Beispiele für Prozeduren mit verschiedenen Parameter-Modi gesehen.

---

# **Schritt 8: Code, der Werte zurückgibt (Stored Functions)**

Eine **Stored Function** ist, ähnlich wie eine Prozedur, ein benannter, kompilierter und in der Datenbank gespeicherter PL/SQL-Block. Der entscheidende Unterschied ist: Eine Funktion **muss immer genau einen Wert** eines bestimmten Datentyps zurückgeben.

**Unterschiede zwischen Funktionen und Prozeduren:**

* **Rückgabewert:** Funktionen geben über das `RETURN`-Schlüsselwort einen Wert zurück. Prozeduren geben keinen direkten Wert zurück (können aber `OUT`-Parameter verwenden, um Ergebnisse zu liefern).
* **Zweck:** Funktionen werden typischerweise für Berechnungen, Datenvalidierungen oder das Abrufen eines einzelnen Datums verwendet, das dann weiterverarbeitet wird. Prozeduren werden eher verwendet, um eine Aktion oder eine Reihe von Aktionen durchzuführen.
* **Verwendung in SQL:** Der größte Vorteil von Funktionen ist, dass sie (unter bestimmten Bedingungen, z.B. dürfen sie keine Daten ändern) direkt in SQL-Anweisungen (`SELECT`, `WHERE`, `GROUP BY`, `ORDER BY` etc.) verwendet werden können, ähnlich wie eingebaute Funktionen (z.B. `UPPER`, `SUBSTR`, `SYSDATE`). Prozeduren können nicht direkt in SQL-Queries aufgerufen werden.

**Erstellen einer Funktion (`CREATE FUNCTION`)**

Die Syntax ähnelt der von Prozeduren, hat aber zwei wichtige Ergänzungen:

```sql
CREATE [OR REPLACE] FUNCTION funktions_name
  [(parameter1 [IN] datentyp [DEFAULT wert], -- Parameter sind fast immer nur IN!
    parameter2 [IN] datentyp [DEFAULT wert],
    ... )]
RETURN rueckgabe_datentyp -- WICHTIG: Datentyp des Rückgabewerts
IS -- oder AS
  -- Deklarationsteil
BEGIN
  -- Ausführungsteil
  -- ... Logik zur Berechnung/Ermittlung des Werts ...

  RETURN wert_der_zurueckgegeben_wird; -- WICHTIG: Gibt den Wert zurück und beendet die Funktion

  -- Es kann mehrere RETURN-Anweisungen geben (z.B. in IF-Zweigen),
  -- aber es muss sichergestellt sein, dass auf jedem logischen Pfad,
  -- der die Funktion normal beendet, ein RETURN ausgeführt wird.

EXCEPTION
  -- Optional: Exception-Handler
  WHEN OTHERS THEN
     -- Gute Praxis: Auch im Fehlerfall einen Wert zurückgeben (oft NULL)
     -- oder den Fehler weiterleiten (RAISE)
     RETURN NULL; -- Gibt NULL zurück, wenn ein Fehler auftritt
END [funktions_name];
/
```

* **Parameter:** Funktionen sollten idealerweise nur `IN`-Parameter haben. Technisch sind `OUT` und `IN OUT` möglich, aber sie widersprechen dem Konzept einer Funktion (Rückgabe *eines* Wertes) und verhindern meist die Verwendung der Funktion in SQL. Also: **Verwende für Funktionen nur `IN`-Parameter!**
* `RETURN rueckgabe_datentyp`: Diese Zeile im Funktionskopf (Header) ist obligatorisch und legt fest, welchen Datentyp der Wert hat, den die Funktion zurückgibt.
* `RETURN wert_der_zurueckgegeben_wird;`: Diese Anweisung im `BEGIN`- oder `EXCEPTION`-Block beendet die Ausführung der Funktion und liefert den angegebenen Wert an den Aufrufer zurück. Der Typ von `wert_der_zurueckgegeben_wird` muss mit `rueckgabe_datentyp` kompatibel sein.

**Aufrufen einer Funktion**

Funktionen werden aufgerufen, indem ihr Name (mit den erforderlichen Argumenten) überall dort verwendet wird, wo ein Ausdruck des Rückgabetyps erwartet wird:

1.  **In PL/SQL:**
    ```sql
    DECLARE
      v_ergebnis funktionen_name%RETURN; -- Datentyp vom Rückgabewert holen (Best Practice!)
      -- oder: v_ergebnis rueckgabe_datentyp;
    BEGIN
      v_ergebnis := funktions_name(argument1, argument2, ...); -- Zuweisung an Variable

      IF funktions_name(argument1, ...) > 10 THEN -- In Bedingungen
        -- ...
      END IF;
    END;
    /
    ```
2.  **In SQL (der häufigste Anwendungsfall):**
    ```sql
    -- In der SELECT-Liste
    SELECT spalte1, spalte2, funktions_name(spalte3) AS berechneter_wert
      FROM tabelle;

    -- In der WHERE-Klausel
    SELECT spalte1
      FROM tabelle
     WHERE funktions_name(spalte2) = 'GEWÜNSCHTER WERT';

    -- In ORDER BY, GROUP BY etc.
    ```
    **Wichtige Einschränkung für SQL:** Damit eine Funktion in SQL verwendet werden darf, darf sie standardmäßig den Datenbankstatus nicht ändern (kein `INSERT`, `UPDATE`, `DELETE`) und bestimmte andere Regeln einhalten (Stichwort "Purity Level"). Funktionen, die nur Daten lesen, sind normalerweise unproblematisch.

**Beispiele:**

**1. Einfache Berechnungsfunktion**
Berechnet einen Bonus basierend auf Gehalt und Prozentsatz.

```sql
CREATE OR REPLACE FUNCTION berechne_bonus (
  p_gehalt     IN NUMBER,
  p_prozent    IN NUMBER DEFAULT 0.10 -- 10% Standardbonus
)
RETURN NUMBER -- Gibt eine Zahl (den Bonusbetrag) zurück
IS
  v_bonus NUMBER := 0;
BEGIN
  -- Prüfen auf sinnvolle Eingaben (optional, aber gut)
  IF p_gehalt IS NULL OR p_gehalt < 0 OR p_prozent IS NULL OR p_prozent < 0 THEN
    RETURN 0; -- Kein Bonus bei ungültigen Eingaben
  END IF;

  v_bonus := p_gehalt * p_prozent;

  RETURN ROUND(v_bonus, 2); -- Runde auf 2 Nachkommastellen und gib den Wert zurück

EXCEPTION
  WHEN OTHERS THEN
    -- Bei unerwarteten Fehlern (z.B. numerischer Überlauf) 0 zurückgeben
    RETURN 0;
END berechne_bonus;
/

-- Aufrufe:
SET SERVEROUTPUT ON;

DECLARE
  v_gehalt_test NUMBER := 3000;
  v_bonus_test  NUMBER;
BEGIN
  -- Aufruf aus PL/SQL
  v_bonus_test := berechne_bonus(v_gehalt_test); -- Mit Default-Prozentsatz
  DBMS_OUTPUT.PUT_LINE('Bonus für ' || v_gehalt_test || ' (Standard): ' || v_bonus_test);

  v_bonus_test := berechne_bonus(p_gehalt => 5000, p_prozent => 0.15); -- Mit 15%
  DBMS_OUTPUT.PUT_LINE('Bonus für 5000 (15%): ' || v_bonus_test);
END;
/

-- Aufruf aus SQL (Beispiel mit der Mitarbeiter-Tabelle)
SELECT nachname, gehalt, berechne_bonus(gehalt) AS standard_bonus,
       berechne_bonus(gehalt, 0.05) AS kleiner_bonus
  FROM mitarbeiter
 WHERE abtnr = 20;
```

**2. Funktion zur Datenabfrage**
Gibt den Abteilungsnamen für eine gegebene Abteilungsnummer zurück (angenommen, es gibt `ABTEILUNG` mit `ABTNR`, `NAME`).

```sql
CREATE OR REPLACE FUNCTION get_abteilungs_name (
  p_abtnr IN abteilung.abtnr%TYPE
)
RETURN abteilung.name%TYPE -- Gibt den Namen (VARCHAR2) zurück
IS
  v_abt_name abteilung.name%TYPE;
BEGIN
  SELECT name
    INTO v_abt_name
    FROM abteilung
   WHERE abtnr = p_abtnr;

  RETURN v_abt_name; -- Gibt den gefundenen Namen zurück

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    RETURN 'Unbekannte Abteilung'; -- Alternativwert bei nicht gefundener Abteilung
  WHEN OTHERS THEN
    RETURN 'Fehler bei Abteilungsabfrage'; -- Alternativwert bei anderem Fehler
END get_abteilungs_name;
/

-- Aufrufe:
SET SERVEROUTPUT ON;
DECLARE
  v_name abteilung.name%TYPE;
BEGIN
  -- Aufruf aus PL/SQL
  v_name := get_abteilungs_name(10);
  DBMS_OUTPUT.PUT_LINE('Abt 10: ' || v_name);
  v_name := get_abteilungs_name(99);
  DBMS_OUTPUT.PUT_LINE('Abt 99: ' || v_name);
END;
/

-- Aufruf aus SQL (sehr nützlich!)
SELECT m.nachname, m.job, m.abtnr,
       get_abteilungs_name(m.abtnr) AS abteilungsname -- Funktion hier aufrufen
  FROM mitarbeiter m
ORDER BY abteilungsname, m.nachname;
```

**3. Boolesche Funktion (Hinweis zur SQL-Nutzung)**
Prüft, ob ein Mitarbeiter existiert.

```sql
CREATE OR REPLACE FUNCTION existiert_mitarbeiter (
  p_mnr IN mitarbeiter.mnr%TYPE
)
RETURN BOOLEAN -- Gibt TRUE oder FALSE zurück
IS
  v_dummy NUMBER;
BEGIN
  SELECT 1 -- Wähle irgendeine Konstante
    INTO v_dummy
    FROM mitarbeiter
   WHERE mnr = p_mnr;

  RETURN TRUE; -- Wenn SELECT erfolgreich war (Zeile gefunden), gib TRUE zurück

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    RETURN FALSE; -- Wenn SELECT fehlschlug (keine Zeile), gib FALSE zurück
  WHEN OTHERS THEN
    RETURN FALSE; -- Oder NULL, oder RAISE; je nach Anforderung
END existiert_mitarbeiter;
/

-- Aufruf:
SET SERVEROUTPUT ON;
DECLARE
   v_mnr_test mitarbeiter.mnr%TYPE := 7788;
BEGIN
  -- Aufruf aus PL/SQL (BOOLEAN ist hier direkt verwendbar)
  IF existiert_mitarbeiter(v_mnr_test) THEN
     DBMS_OUTPUT.PUT_LINE('Mitarbeiter ' || v_mnr_test || ' existiert.');
  ELSE
     DBMS_OUTPUT.PUT_LINE('Mitarbeiter ' || v_mnr_test || ' existiert NICHT.');
  END IF;

  -- Aufruf in SQL ist NICHT direkt möglich, da SQL BOOLEAN nicht kennt:
  -- SELECT nachname FROM mitarbeiter WHERE existiert_mitarbeiter(mnr); -- << FEHLER!

  -- Workaround in SQL (z.B. mit CASE oder Vergleich mit einer Funktion, die 0/1 zurückgibt):
  -- Man würde eher eine Funktion schreiben, die 1 (für TRUE) oder 0 (für FALSE) zurückgibt.
  /* Beispiel für alternative Funktion für SQL:
     CREATE OR REPLACE FUNCTION existiert_mitarbeiter_sql (p_mnr IN NUMBER) RETURN NUMBER IS ...
     BEGIN ... RETURN 1; EXCEPTION WHEN NO_DATA_FOUND THEN RETURN 0; END; /
     -- Dann in SQL:
     SELECT nachname FROM mitarbeiter WHERE existiert_mitarbeiter_sql(mnr) = 1;
  */
END;
/
```

**Zusammenfassung Schritt 8:**

* Du verstehst, dass eine Funktion einen einzelnen Wert über die `RETURN`-Anweisung zurückgeben **muss**.
* Du kennst die `CREATE OR REPLACE FUNCTION`-Syntax, inklusive der `RETURN rueckgabe_datentyp`-Klausel im Header.
* Du weißt, dass Funktionen idealerweise nur `IN`-Parameter verwenden sollten.
* Du verstehst den Unterschied zwischen Funktionen und Prozeduren, insbesondere bezüglich des Rückgabewerts und der Verwendbarkeit in SQL.
* Du hast gesehen, wie Funktionen in PL/SQL und (mit Einschränkungen) in SQL-Anweisungen aufgerufen werden können.
* Du bist dir bewusst, dass `BOOLEAN`-Funktionen in SQL nicht direkt verwendet werden können.

---

Okay, nachdem wir nun Prozeduren und Funktionen als einzelne, gespeicherte Objekte kennengelernt haben, kommen wir zu einem der mächtigsten Organisationskonzepte in PL/SQL: **Packages**.

**Schritt 9: Code organisieren mit Packages (Einführung)**

Ein **Package** (Paket) in PL/SQL ist ein Schema-Objekt, das als Container dient, um logisch zusammengehörige PL/SQL-Elemente zu gruppieren. Du kannst darin Prozeduren, Funktionen, Variablen, Konstanten, Cursor, benutzerdefinierte Datentypen (Records, Collections) und Exceptions zusammenfassen.

**Warum Packages verwenden?**

* **Organisation & Modularität:** Hält Code übersichtlich, indem z.B. alle Routinen zur Verwaltung von Mitarbeitern in einem `mitarbeiter_pkg` gebündelt werden.
* **Kapselung (Information Hiding):** Packages haben zwei Teile – eine öffentliche **Spezifikation** (was nach außen sichtbar ist) und einen privaten **Body** (die Implementierungsdetails). Du kannst steuern, was Benutzer des Pakets sehen und verwenden dürfen und was intern verborgen bleibt.
* **Bessere Performance:** Wenn ein Element eines Pakets zum ersten Mal aufgerufen wird, wird das gesamte Paket (oder zumindest Teile davon) in den Speicher der Oracle-Session geladen. Nachfolgende Aufrufe von Elementen desselben Pakets können dann schneller sein.
* **Session-übergreifender Zustand:** Variablen, die im Paket deklariert werden, behalten ihren Wert für die Dauer einer Datenbank-Session (mehr dazu später).
* **Überladen (Overloading):** Innerhalb eines Pakets können Prozeduren oder Funktionen denselben Namen haben, solange sie sich in ihrer Parameterliste (Anzahl, Typ oder Reihenfolge der Parameter) unterscheiden.

**Die Struktur eines Packages**

Ein Package besteht immer aus mindestens einem Teil, meistens aber aus zwei:

1.  **Package Specification (Spezifikation oder Header):**
    * Definiert die **öffentliche Schnittstelle** des Pakets – das, was von außen sichtbar und aufrufbar ist.
    * Enthält die Deklarationen (aber nicht die Implementierung) von öffentlichen Prozeduren und Funktionen.
    * Enthält Deklarationen von öffentlichen Variablen, Konstanten, Typen, Exceptions und Cursorn.
    * Syntax: `CREATE [OR REPLACE] PACKAGE package_name IS | AS ... END [package_name]; /`
    * Muss *vor* dem Package Body erstellt werden.

2.  **Package Body (Rumpf):**
    * Enthält die **Implementierung** (den Code) der Prozeduren und Funktionen, die in der Spezifikation deklariert wurden.
    * Kann zusätzlich *private* Elemente (Prozeduren, Funktionen, Variablen etc.) enthalten, die nur *innerhalb* des Package Bodys sichtbar und verwendbar sind. Diese sind von außen verborgen.
    * Syntax: `CREATE [OR REPLACE] PACKAGE BODY package_name IS | AS ... END [package_name]; /`
    * Ein Body ist nur notwendig, wenn die Spezifikation Prozeduren oder Funktionen deklariert (oder wenn private Elemente oder ein Initialisierungsblock benötigt werden). Wenn die Spezifikation nur Typen, Variablen, Konstanten etc. enthält, ist kein Body erforderlich.
    * Kann nur erstellt werden, wenn die zugehörige Spezifikation bereits existiert.

**Aufrufen von Package-Elementen**

Auf öffentliche Elemente eines Pakets greifst du mit der Punkt-Notation zu: `package_name.element_name`

* `package_name.prozedur_name(argumente);`
* `variable := package_name.funktion_name(argumente);`
* `wert := package_name.oeffentliche_variable;`
* `mein_typ := package_name.oeffentlicher_typ;`
* `RAISE package_name.oeffentliche_exception;`

**Beispiel: Ein einfaches Utility-Package**

Wir erstellen ein Paket `utils_pkg`, das einige unserer vorherigen Beispiele (leicht angepasst) und eine neue Funktion enthält.

**1. Package Specification (`utils_pkg` Spezifikation):**
Hier deklarieren wir, was das Paket nach außen anbietet.

```sql
CREATE OR REPLACE PACKAGE utils_pkg
IS -- Beginn der öffentlichen Schnittstelle

  -- Eine öffentliche Konstante
  c_app_version CONSTANT VARCHAR2(10) := '1.0.1'; -- Aktuelle Version (Stand: April 2025)

  -- Eine öffentliche Exception
  e_ungueltige_eingabe EXCEPTION;

  -- Spezifikation der Prozedur zum Loggen (nur der Kopf)
  PROCEDURE log_meldung (
    p_nachricht IN VARCHAR2,
    p_level     IN VARCHAR2 DEFAULT 'INFO'
  );

  -- Spezifikation der Funktion zur Bonusberechnung (nur der Kopf)
  FUNCTION berechne_bonus (
    p_gehalt     IN NUMBER,
    p_prozent    IN NUMBER DEFAULT 0.10
  ) RETURN NUMBER;

  -- Spezifikation einer neuen Funktion zur E-Mail-Validierung (sehr einfach)
  FUNCTION ist_email_gueltig (
    p_email IN VARCHAR2
  ) RETURN BOOLEAN;

END utils_pkg; -- Ende der Spezifikation
/
```
*Jetzt ist die Schnittstelle definiert. Man könnte dieses Paket bereits verwenden, um auf `c_app_version` oder `e_ungueltige_eingabe` zuzugreifen, aber die Prozeduren/Funktionen könnten noch nicht aufgerufen werden, da ihre Implementierung fehlt.*

**2. Package Body (`utils_pkg` Rumpf):**
Hier implementieren wir den Code für die in der Spezifikation deklarierten Prozeduren und Funktionen.

```sql
CREATE OR REPLACE PACKAGE BODY utils_pkg
IS -- Beginn der Implementierung

  -- Implementierung der Prozedur log_meldung
  PROCEDURE log_meldung (
    p_nachricht IN VARCHAR2,
    p_level     IN VARCHAR2 DEFAULT 'INFO'
  )
  IS
    -- Private Variable, nur innerhalb dieser Prozedur sichtbar
    v_timestamp_txt VARCHAR2(100);
  BEGIN
    v_timestamp_txt := TO_CHAR(SYSTIMESTAMP, 'YYYY-MM-DD HH24:MI:SS.FF3 TZR');
    DBMS_OUTPUT.PUT_LINE(
      v_timestamp_txt || ' [' || UPPER(p_level) || '] ' || p_nachricht
    );
  END log_meldung; -- Ende der Implementierung von log_meldung


  -- Implementierung der Funktion berechne_bonus
  FUNCTION berechne_bonus (
    p_gehalt     IN NUMBER,
    p_prozent    IN NUMBER DEFAULT 0.10
  ) RETURN NUMBER
  IS
    v_bonus NUMBER := 0;
  BEGIN
    IF p_gehalt IS NULL OR p_gehalt < 0 OR p_prozent IS NULL OR p_prozent < 0 THEN
      -- Hier verwenden wir die im Package deklarierte Exception
      RAISE e_ungueltige_eingabe;
    END IF;

    v_bonus := p_gehalt * p_prozent;
    RETURN ROUND(v_bonus, 2);

  EXCEPTION
    -- Fangen der eigenen Exception
    WHEN e_ungueltige_eingabe THEN
      log_meldung('Ungültige Eingabe für berechne_bonus erhalten.', 'WARN');
      RETURN 0; -- Oder NULL, je nach Anforderung
    WHEN OTHERS THEN
      log_meldung('Unerwarteter Fehler in berechne_bonus: ' || SQLERRM, 'ERROR');
      RETURN NULL; -- Sicherer Rückgabewert bei Fehlern
  END berechne_bonus; -- Ende der Implementierung von berechne_bonus


  -- Implementierung der Funktion ist_email_gueltig
  FUNCTION ist_email_gueltig (
    p_email IN VARCHAR2
  ) RETURN BOOLEAN
  IS
    -- Sehr einfache Prüfung: Muss ein '@' und einen '.' nach dem '@' enthalten
  BEGIN
    IF p_email IS NULL THEN
      RETURN FALSE;
    END IF;

    -- Prüft, ob '@' vorkommt und ob danach ein '.' vorkommt
    RETURN INSTR(p_email, '@') > 0 AND INSTR(p_email, '.', INSTR(p_email, '@')) > 0;

    -- Eine robustere Prüfung würde reguläre Ausdrücke verwenden:
    -- RETURN REGEXP_LIKE(p_email, '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');
  END ist_email_gueltig; -- Ende der Implementierung von ist_email_gueltig

  -- Hier könnten noch private Prozeduren/Funktionen stehen,
  -- die nur von log_meldung, berechne_bonus oder ist_email_gueltig aufgerufen werden.

END utils_pkg; -- Ende des Package Bodys
/
```

**3. Verwendung des Packages:**

```sql
SET SERVEROUTPUT ON;

DECLARE
  v_gehalt     NUMBER := 4000;
  v_bonus      NUMBER;
  v_mail_ok    BOOLEAN;
  v_test_mail1 VARCHAR2(100) := 'test@example.com';
  v_test_mail2 VARCHAR2(100) := 'invalid-email';
BEGIN
  -- Zugriff auf die Konstante
  utils_pkg.log_meldung('Verwende Utils App Version: ' || utils_pkg.c_app_version);

  -- Aufruf der Funktion im Package
  v_bonus := utils_pkg.berechne_bonus(p_gehalt => v_gehalt, p_prozent => 0.08);
  utils_pkg.log_meldung('Bonus für ' || v_gehalt || ' (8%): ' || v_bonus);

  -- Aufruf der E-Mail-Validierung
  v_mail_ok := utils_pkg.ist_email_gueltig(v_test_mail1);
  utils_pkg.log_meldung('E-Mail "' || v_test_mail1 || '" ist gültig: ' || CASE WHEN v_mail_ok THEN 'Ja' ELSE 'Nein' END);

  v_mail_ok := utils_pkg.ist_email_gueltig(v_test_mail2);
  utils_pkg.log_meldung('E-Mail "' || v_test_mail2 || '" ist gültig: ' || CASE WHEN v_mail_ok THEN 'Ja' ELSE 'Nein' END);

  -- Beispiel: Fehler auslösen und fangen
  BEGIN
     v_bonus := utils_pkg.berechne_bonus(p_gehalt => -100); -- Löst e_ungueltige_eingabe aus
     utils_pkg.log_meldung('Bonus (ungültig): ' || v_bonus); -- Wird hier nicht erreicht
  EXCEPTION
     WHEN utils_pkg.e_ungueltige_eingabe THEN -- Fangen der Package-Exception
       utils_pkg.log_meldung('Eigene Fehlerbehandlung: Ungültige Eingabe für Bonus.', 'ERROR');
  END;

END;
/
```

**Zusammenfassung Schritt 9:**

* Du verstehst, dass Packages dazu dienen, zusammengehörige PL/SQL-Elemente zu gruppieren.
* Du kennst die Vorteile: Organisation, Kapselung, Performance, Session-Zustand, Überladen.
* Du kennst die zwei Teile eines Pakets: die **Spezifikation** (öffentliche Schnittstelle) und den **Body** (Implementierung, private Elemente).
* Du weißt, wie man auf öffentliche Package-Elemente zugreift ( `package_name.element_name`).
* Du hast ein Beispiel gesehen, wie man Konstanten, Exceptions, Prozeduren und Funktionen in einem Paket definiert und implementiert.

---

 # TESTSTOFF -ab hier -
 
---

# **Schritt 10: Einführung in Trigger – Automatisierte Aktionen**

**Was ist ein Trigger?**

Ein Trigger (Auslöser) ist ein benannter PL/SQL-Block (oder eine Referenz auf eine Java-/C-Routine), der **automatisch** von der Oracle-Datenbank ausgeführt wird, wenn ein bestimmtes Ereignis eintritt. Du schreibst den Code einmal, und Oracle sorgt dafür, dass er zum richtigen Zeitpunkt "feuert".

**Wann werden Trigger ausgelöst? (Ereignisse)**

Die häufigsten Ereignisse sind:

1.  **DML-Ereignisse (Data Manipulation Language):** Das Einfügen (`INSERT`), Ändern (`UPDATE`) oder Löschen (`DELETE`) von Daten in einer bestimmten **Tabelle** oder einem **View**. Dies ist der häufigste Anwendungsfall.
2.  **DDL-Ereignisse (Data Definition Language):** Das Erstellen (`CREATE`), Ändern (`ALTER`) oder Löschen (`DROP`) von Schema-Objekten (Tabellen, Indizes, Prozeduren etc.). Nützlich z.B. für Auditing von Schema-Änderungen.
3.  **Datenbank-Ereignisse (System Events):** Ereignisse wie das Starten (`STARTUP`) oder Herunterfahren (`SHUTDOWN`) der Datenbank, Benutzeranmeldungen (`LOGON`) oder -abmeldungen (`LOGOFF`) oder das Auftreten bestimmter Fehler (`SERVERERROR`).

Wir konzentrieren uns zunächst auf **DML-Trigger auf Tabellen**, da sie am gebräuchlichsten sind und die Basis für deine spezifischen Fragen bilden.

**Wofür werden DML-Trigger verwendet?**

* **Komplexe Geschäftsregeln durchsetzen:** Wenn die Logik über das hinausgeht, was mit Standard-Constraints (PRIMARY KEY, FOREIGN KEY, CHECK, NOT NULL) möglich ist (z.B. Prüfungen über mehrere Tabellen hinweg).
* **Auditing / Protokollierung:** Automatische Aufzeichnung von Datenänderungen (Wer hat was wann geändert?).
* **Datenintegrität / Konsistenz wahren:** Automatische Aktualisierung abgeleiteter oder redundanter Daten (z.B. Pflegen von Summen in einer anderen Tabelle). *Vorsicht: Oft gibt es bessere Alternativen wie Materialized Views oder virtuelle Spalten.*
* **Abgeleitete Daten generieren:** Automatische Befüllung von Spaltenwerten (z.B. Setzen eines `created_by` / `creation_date` Zeitstempels).
* **Ungültige Operationen verhindern:** Abfangen von DML-Operationen und ggf. Auslösen eines Fehlers unter bestimmten Bedingungen.

**Grundlegende Struktur eines DML-Triggers**

```sql
CREATE [OR REPLACE] TRIGGER trigger_name
  -- 1. Timing: Wann soll der Trigger feuern?
  { BEFORE | AFTER | INSTEAD OF }
  -- 2. Event(s): Bei welchem DML-Ereignis?
  { INSERT [OR UPDATE [OF spalte1, spalte2,...] OR DELETE] }
  -- 3. Target: Auf welcher Tabelle oder View?
  ON tabelle_oder_view_name
  -- Optional: Alias für :OLD und :NEW (selten geändert)
  [ REFERENCING OLD AS old_alias NEW AS new_alias ]
  -- 4. Granularität: Einmal pro Statement oder pro betroffener Zeile?
  [ FOR EACH ROW ] -- Wenn weggelassen -> Statement-Level
  -- Optional: Bedingung für Row-Level Trigger
  [ WHEN (bedingung) ]
-- Optional: Initialer Status
[ ENABLE | DISABLE ]
-- Optional: Deklarationsteil
DECLARE
  -- Lokale Variablen etc.
-- 5. Trigger Body: Was soll passieren?
BEGIN
  -- PL/SQL Code
  -- Zugriff auf :OLD und :NEW Werte (nur bei FOR EACH ROW)
EXCEPTION
  -- Optional: Fehlerbehandlung
END [trigger_name];
/
```

**Schlüsselelemente erklärt:**

1.  **Timing:**
    * `BEFORE`: Feuert, *bevor* die DML-Operation ausgeführt wird. Gut für:
        * Validierung von Daten (bevor sie geschrieben werden).
        * Modifikation von Daten (`:NEW`-Werte ändern), bevor sie eingefügt/aktualisiert werden.
        * Verhindern der Operation durch Auslösen eines Fehlers.
    * `AFTER`: Feuert, *nachdem* die DML-Operation erfolgreich abgeschlossen wurde (und Constraints geprüft wurden). Gut für:
        * Protokollierung der durchgeführten Änderung.
        * Auslösen von Folgeaktionen, die auf der erfolgreichen Änderung basieren.
    * `INSTEAD OF`: Ein Spezialfall **nur für Views**. Feuert *anstelle* der DML-Operation auf dem View. Wird verwendet, um Views änderbar zu machen, die es normalerweise nicht sind (z.B. Views über mehrere Tabellen ohne Key-Preserved Rows). **(Das ist Teil deiner spezifischen Fragen, wir kommen darauf zurück!)**
2.  **Event(s):** `INSERT`, `UPDATE`, `DELETE`. Man kann mehrere mit `OR` kombinieren. `UPDATE OF spalte` feuert nur, wenn mindestens eine der genannten Spalten im `SET`-Teil des `UPDATE`-Statements vorkommt.
3.  **Target:** Der Name der Tabelle oder des Views, auf den der Trigger reagieren soll.
4.  **Granularität (Level):**
    * **Statement-Level (Standard, wenn `FOR EACH ROW` fehlt):** Der Trigger feuert genau **einmal** pro auslösendem DML-Statement (z.B. einmal für `UPDATE emp SET sal = sal * 1.1 WHERE deptno = 10;`), unabhängig davon, wie viele Zeilen betroffen sind (auch wenn 0 Zeilen betroffen sind). Hat **keinen** Zugriff auf `:OLD`- oder `:NEW`-Werte.
    * **Row-Level (`FOR EACH ROW` angegeben):** Der Trigger feuert **für jede einzelne Zeile**, die von der DML-Anweisung betroffen ist. Wenn ein `UPDATE` 10 Zeilen ändert, feuert der Trigger 10 Mal. Hat Zugriff auf die Daten der *aktuell verarbeiteten Zeile* über `:OLD` und `:NEW`.
5.  **Korrelationsnamen `:OLD` und `:NEW` (Nur bei `FOR EACH ROW` auf Tabellen/Views):**
    * Sind wie spezielle Records, die die Spaltenwerte der Zeile enthalten.
    * `:NEW`: Repräsentiert den **neuen** Zustand der Zeile.
        * Bei `INSERT`: Die Werte, die eingefügt werden sollen. Können im `BEFORE`-Trigger geändert werden.
        * Bei `UPDATE`: Die Werte, *nachdem* das Update angewendet wurde. Können im `BEFORE`-Trigger geändert werden.
        * Bei `DELETE`: Nicht verfügbar (`:NEW`-Werte sind `NULL`).
    * `:OLD`: Repräsentiert den **alten** Zustand der Zeile (vor der Änderung).
        * Bei `INSERT`: Nicht verfügbar (`:OLD`-Werte sind `NULL`).
        * Bei `UPDATE`: Die Werte, *bevor* das Update angewendet wurde. Schreibgeschützt.
        * Bei `DELETE`: Die Werte der Zeile, die gelöscht wird. Schreibgeschützt.
    * Zugriff: `:NEW.spaltenname`, `:OLD.spaltenname`.
6.  **`WHEN (bedingung)` (Nur bei `FOR EACH ROW`):** Eine zusätzliche Bedingung (ähnlich einer `WHERE`-Klausel). Der Trigger-Body (`BEGIN...END`) wird nur für die Zeilen ausgeführt, bei denen diese `WHEN`-Bedingung `TRUE` ist. Das kann effizienter sein, als die Bedingung erst im `BEGIN`-Block zu prüfen, wenn nur wenige Zeilen die Bedingung erfüllen.

**Wichtige Hinweise und Best Practices:**

* **Sparsam verwenden:** Trigger können die Wartbarkeit und das Verständnis des Systemverhaltens erschweren. Nutze sie nur, wenn es keine einfacheren Alternativen (Constraints, Default-Werte, virtuelle Spalten, Anwendungslogik) gibt.
* **Einfach halten:** Halte die Logik im Trigger kurz und effizient. Lagere komplexe Logik in separate (gepackte) Prozeduren aus, die vom Trigger aufgerufen werden.
* **Performance:** Vermeide komplexe Abfragen innerhalb von Row-Level-Triggern, da sie für jede betroffene Zeile ausgeführt werden.
* **Kaskadierende Trigger:** Ein Trigger führt DML aus, was einen anderen Trigger auslöst usw. Das kann schnell unübersichtlich werden. Oracle hat Mechanismen (z.B. "Mutating Table Error"), um endlose Rekursionen oder inkonsistente Zustände zu verhindern, aber das Design sollte solche Kaskaden vermeiden.
* **Transaktionen:** Trigger laufen innerhalb derselben Transaktion wie das auslösende DML-Statement. Wenn der Trigger fehlschlägt (eine unbehandelte Exception auslöst), wird das gesamte Statement (inklusive aller Aktionen des Triggers bis zum Fehler) zurückgerollt (`ROLLBACK`). Trigger sollten daher in der Regel **kein** `COMMIT` oder `ROLLBACK` enthalten.

**Beispiel: Audit-Trigger für Gehaltsänderungen**

Wir erstellen einen Trigger, der Änderungen am Gehalt (`GEHALT`), Neueinstellungen und Löschungen in der `MITARBEITER`-Tabelle protokolliert. Dazu brauchen wir eine Audit-Tabelle:

```sql
-- 1. Audit-Tabelle erstellen (falls noch nicht vorhanden)
CREATE TABLE mitarbeiter_audit (
  audit_id      NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY, -- Automatische ID
  geaendert_von VARCHAR2(100),  -- Wer hat geändert?
  geaendert_am  TIMESTAMP,      -- Wann wurde geändert?
  mnr           mitarbeiter.mnr%TYPE, -- Welche Mitarbeiter-Nr?
  aktion        VARCHAR2(10),   -- Was wurde getan (INSERT, UPDATE, DELETE)?
  altes_gehalt  mitarbeiter.gehalt%TYPE,
  neues_gehalt  mitarbeiter.gehalt%TYPE
);

-- 2. Der Trigger
CREATE OR REPLACE TRIGGER trg_mitarbeiter_gehalt_audit
  AFTER INSERT OR DELETE OR UPDATE OF gehalt ON mitarbeiter -- Timing, Event(s), Target
  FOR EACH ROW -- Wir brauchen :OLD/:NEW Werte -> Row-Level
DECLARE
  v_benutzer mitarbeiter_audit.geaendert_von%TYPE := USER; -- Aktueller DB-Benutzer
BEGIN
  -- Prüfen, welche Aktion den Trigger ausgelöst hat
  IF INSERTING THEN
    -- Bei INSERT gibt es nur :NEW
    INSERT INTO mitarbeiter_audit (geaendert_von, geaendert_am, mnr, aktion, altes_gehalt, neues_gehalt)
    VALUES (v_benutzer, SYSTIMESTAMP, :NEW.mnr, 'INSERT', NULL, :NEW.gehalt);

  ELSIF UPDATING('gehalt') THEN -- Nur wenn sich das Gehalt tatsächlich ändert
    -- Bei UPDATE gibt es :OLD und :NEW
    -- Nur loggen, wenn sich das Gehalt wirklich geändert hat (optional, aber sinnvoll)
    IF :OLD.gehalt <> :NEW.gehalt OR (:OLD.gehalt IS NULL AND :NEW.gehalt IS NOT NULL) OR (:OLD.gehalt IS NOT NULL AND :NEW.gehalt IS NULL) THEN
       INSERT INTO mitarbeiter_audit (geaendert_von, geaendert_am, mnr, aktion, altes_gehalt, neues_gehalt)
       VALUES (v_benutzer, SYSTIMESTAMP, :OLD.mnr, 'UPDATE', :OLD.gehalt, :NEW.gehalt);
    END IF;

  ELSIF DELETING THEN
    -- Bei DELETE gibt es nur :OLD
    INSERT INTO mitarbeiter_audit (geaendert_von, geaendert_am, mnr, aktion, altes_gehalt, neues_gehalt)
    VALUES (v_benutzer, SYSTIMESTAMP, :OLD.mnr, 'DELETE', :OLD.gehalt, NULL);
  END IF;

END trg_mitarbeiter_gehalt_audit;
/

-- Demonstration:
-- Führe einige DMLs aus und prüfe dann die Audit-Tabelle
/*
-- Beispiel-DML (Annahme: Es gibt Mitarbeiter 7788 und Abteilung 10)
INSERT INTO mitarbeiter (mnr, nachname, job, abtnr, gehalt) VALUES (9999, 'TESTER', 'ANALYST', 10, 2500);
UPDATE mitarbeiter SET gehalt = 3200 WHERE mnr = 7788;
UPDATE mitarbeiter SET job = 'CLERK' WHERE mnr = 7788; -- Löst den Trigger NICHT aus, da 'gehalt' nicht geändert wird
DELETE FROM mitarbeiter WHERE mnr = 9999;
COMMIT; -- Änderungen und Audit-Einträge speichern

-- Audit-Tabelle ansehen
SELECT * FROM mitarbeiter_audit ORDER BY geaendert_am;
*/
```

**Zusammenfassung Schritt 10:**

* Du verstehst, dass Trigger automatisch auf Datenbankereignisse (v.a. DML) reagieren.
* Du kennst die Hauptkomponenten eines Triggers: Timing (`BEFORE`, `AFTER`, `INSTEAD OF`), Event (`INSERT`, `UPDATE`, `DELETE`), Target (Tabelle/View), Granularität (Statement vs. `FOR EACH ROW`) und den Trigger-Body.
* Du verstehst den Unterschied zwischen Statement-Level und Row-Level Triggern.
* Du weißt, wie man mit `:OLD` und `:NEW` auf die Zeilenwerte in Row-Level-Triggern zugreift.
* Du hast ein Beispiel für einen Audit-Trigger gesehen und die Prädikate `INSERTING`, `UPDATING`, `DELETING` kennengelernt.
* Du bist dir der potenziellen Nachteile und Best Practices bei der Verwendung von Triggern bewusst.

---

# **Schritt 11: Trigger Teil 1.1 – INSTEAD OF Trigger**

**Das Problem: Nicht aktualisierbare Views**

Du hast vielleicht schon bemerkt, dass nicht alle Views (Sichten) direkt mit `INSERT`, `UPDATE` oder `DELETE` bearbeitet werden können. Oracle erlaubt DML auf Views nur unter bestimmten Bedingungen. Ein View ist typischerweise **nicht** direkt modifizierbar, wenn er:

* Mehrere Tabellen über einen **JOIN** verbindet (es sei denn, es ist ein "key-preserved join" und die DML-Operation betrifft nur die key-preserved Tabelle).
* Aggregatfunktionen verwendet (`COUNT`, `SUM`, `AVG` etc.).
* `GROUP BY` oder `HAVING`-Klauseln enthält.
* `DISTINCT` verwendet.
* Spalten enthält, die auf Ausdrücken oder Funktionsaufrufen basieren (z.B. `sal * 1.1` oder `UPPER(ename)`).
* Set-Operatoren (`UNION`, `INTERSECT`, `MINUS`) benutzt.

**Die Lösung: INSTEAD OF Trigger**

Genau hier kommen `INSTEAD OF` Trigger ins Spiel. Sie werden **auf einem View** definiert und feuern **anstelle** (`instead of`) der eigentlichen `INSERT`, `UPDATE` oder `DELETE` Operation, die gegen den View ausgeführt wird.

* **Zweck:** Sie ermöglichen es dir, die Logik zu definieren, was in den zugrundeliegenden Basistabellen passieren soll, wenn jemand versucht, den View zu modifizieren. Sie machen also nicht-aktualisierbare Views doch aktualisierbar.
* **Timing:** Immer `INSTEAD OF`.
* **Target:** Immer ein `view_name`.
* **Granularität:** `INSTEAD OF` Trigger arbeiten **implizit immer auf Zeilenebene** (`FOR EACH ROW`), auch wenn man `FOR EACH ROW` nicht explizit hinschreibt (es ist aber gute Praxis, es dennoch zu tun). Das liegt daran, dass der Trigger die DML-Operation für jede Zeile abfangen und übersetzen muss.
* **`:OLD` / `:NEW`:** Funktionieren wie bei Row-Level Triggern auf Tabellen, beziehen sich aber auf die Spalten des **Views**, wie sie in der DML-Anweisung gegen den View verwendet werden.

**Die Logik im Trigger:** Der `BEGIN...END`-Block des Triggers muss den Code enthalten, um die gewünschte Operation (basierend auf `INSERTING`, `UPDATING`, `DELETING` und den `:OLD`/`:NEW`-Werten aus dem View-Kontext) auf den **Basistabellen** durchzuführen.

---

**Beispiel 1: View mit Mitarbeiter und Abteilungsname (deine Anforderung)**

* **Ziel:** Einen View `emp_dept_name_v` erstellen, der Mitarbeiternummer, Name, Job, Gehalt und den **Namen** der Abteilung anzeigt (nicht die Nummer). Dieser View soll für `INSERT` und `UPDATE` nutzbar sein. Beim Einfügen oder Ändern soll anhand des Abteilungsnamens die korrekte `deptno` in der `MITARBEITER`-Tabelle gespeichert werden.

* **Voraussetzungen:** Tabellen `MITARBEITER` (mit `MNR`, `NACHNAME`, `JOB`, `GEHALT`, `ABTNR`) und `ABTEILUNG` (mit `ABTNR`, `NAME`, `ORT`).

* **1. View erstellen:**

```sql
CREATE OR REPLACE VIEW emp_dept_name_v AS
SELECT
    m.mnr,
    m.nachname,
    m.job,
    m.gehalt,
    a.name AS abteilungsname -- Spalte aus ABTEILUNG, kein Fremdschlüssel in MITARBEITER
FROM
    mitarbeiter m
LEFT JOIN -- LEFT JOIN, damit Mitarbeiter ohne Abteilung auch angezeigt werden
    abteilung a ON m.abtnr = a.abtnr;

-- Dieser View ist typischerweise nicht direkt auf 'abteilungsname' aktualisierbar,
-- und INSERT ist schwierig, da 'abtnr' fehlt und 'abteilungsname' berechnet ist.
```

* **2. INSTEAD OF Trigger erstellen:**

```sql
CREATE OR REPLACE TRIGGER trg_emp_dept_name_v_iu -- IU = Insert/Update
   INSTEAD OF INSERT OR UPDATE ON emp_dept_name_v
   FOR EACH ROW
DECLARE
   v_abtnr abteilung.abtnr%TYPE; -- Variable für die gefundene Abteilungsnummer
BEGIN
   -- Was soll bei einem INSERT auf den View passieren?
   IF INSERTING THEN
      -- Finde die Abteilungsnummer basierend auf dem im View angegebenen Namen (:NEW.abteilungsname)
      BEGIN
         SELECT abtnr
           INTO v_abtnr
           FROM abteilung
          WHERE LOWER(name) = LOWER(:NEW.abteilungsname); -- Suche case-insensitiv
      EXCEPTION
         WHEN NO_DATA_FOUND THEN
             -- Wenn kein Abteilung mit dem Namen existiert, Fehler auslösen
             RAISE_APPLICATION_ERROR(-20001, 'Ungültiger Abteilungsname angegeben: "' || :NEW.abteilungsname || '". Abteilung nicht gefunden.');
         WHEN TOO_MANY_ROWS THEN
              -- Sollte bei eindeutigen Namen nicht passieren, aber sicher ist sicher
             RAISE_APPLICATION_ERROR(-20001, 'Abteilungsname "' || :NEW.abteilungsname || '" ist nicht eindeutig.');
      END;

      -- Füge den Datensatz in die Basistabelle MITARBEITER ein
      INSERT INTO mitarbeiter (mnr, nachname, job, gehalt, abtnr)
      VALUES (:NEW.mnr, :NEW.nachname, :NEW.job, :NEW.gehalt, v_abtnr);

   -- Was soll bei einem UPDATE auf den View passieren?
   ELSIF UPDATING THEN
      -- Prüfen, ob sich der Abteilungsname geändert hat oder ob versucht wird, ihn zu ändern.
      -- Wir müssen auch prüfen, ob 'abteilungsname' überhaupt im UPDATE-Statement vorkommt
      -- (dafür gibt es keine direkte UPDATING('view_spalte') Funktion für INSTEAD OF, wir prüfen auf Wertänderung).
      IF :OLD.abteilungsname <> :NEW.abteilungsname
         OR (:OLD.abteilungsname IS NULL AND :NEW.abteilungsname IS NOT NULL)
         OR (:OLD.abteilungsname IS NOT NULL AND :NEW.abteilungsname IS NULL)
      THEN
          -- Ja, der Abteilungsname wurde geändert. Finde die neue Abteilungsnummer.
          BEGIN
             SELECT abtnr
               INTO v_abtnr
               FROM abteilung
              WHERE LOWER(name) = LOWER(:NEW.abteilungsname);
          EXCEPTION
             WHEN NO_DATA_FOUND THEN
                 RAISE_APPLICATION_ERROR(-20002, 'Ungültiger Abteilungsname angegeben: "' || :NEW.abteilungsname || '". Abteilung nicht gefunden.');
             WHEN TOO_MANY_ROWS THEN
                 RAISE_APPLICATION_ERROR(-20002, 'Abteilungsname "' || :NEW.abteilungsname || '" ist nicht eindeutig.');
          END;

          -- Update die Basistabelle MITARBEITER mit den neuen Werten aus dem View
          -- und der gefundenen Abteilungsnummer.
          UPDATE mitarbeiter
             SET nachname = :NEW.nachname,
                 job      = :NEW.job,
                 gehalt   = :NEW.gehalt,
                 abtnr    = v_abtnr -- Setze die gefundene Nummer
           WHERE mnr = :OLD.mnr; -- Identifiziere die Zeile über den Primärschlüssel (:OLD.mnr)

      ELSE
          -- Nein, der Abteilungsname hat sich nicht geändert.
          -- Update nur die anderen Spalten in der Basistabelle MITARBEITER.
          UPDATE mitarbeiter
             SET nachname = :NEW.nachname,
                 job      = :NEW.job,
                 gehalt   = :NEW.gehalt
             -- abtnr bleibt unverändert
           WHERE mnr = :OLD.mnr;
      END IF;
   END IF;

EXCEPTION
    -- Generelle Fehlerbehandlung für unerwartete Fehler
    WHEN OTHERS THEN
        -- Logge den Fehler eventuell
        RAISE; -- Leite den ursprünglichen Fehler weiter
END trg_emp_dept_name_v_iu;
/
```

* **3. Demonstration:**

```sql
SET SERVEROUTPUT ON;
-- Annahme: Es gibt Abteilungen 'RESEARCH' (20) und 'SALES' (30)
-- Erfolgreicher Insert
INSERT INTO emp_dept_name_v (mnr, nachname, job, gehalt, abteilungsname)
VALUES (1001, 'Wagner', 'ANALYST', 3100, 'RESEARCH');
-- Fehlgeschlagener Insert (falscher Abteilungsname)
BEGIN
  INSERT INTO emp_dept_name_v (mnr, nachname, job, gehalt, abteilungsname)
  VALUES (1002, 'Huber', 'CLERK', 1500, 'MARKETING'); -- Angenommen, MARKETING gibt es nicht
EXCEPTION WHEN OTHERS THEN DBMS_OUTPUT.PUT_LINE('Fehler erwartet: ' || SQLERRM);
END;
/
-- Erfolgreiches Update (Gehalt und Abteilung ändern)
UPDATE emp_dept_name_v
   SET gehalt = 3200, abteilungsname = 'SALES'
 WHERE mnr = 1001;
-- Erfolgreiches Update (nur Job ändern)
UPDATE emp_dept_name_v
   SET job = 'SENIOR ANALYST'
 WHERE mnr = 1001;
-- Fehlgeschlagenes Update (falscher Abteilungsname)
BEGIN
 UPDATE emp_dept_name_v
    SET abteilungsname = 'SUPPORT' -- Angenommen, SUPPORT gibt es nicht
  WHERE mnr = 1001;
EXCEPTION WHEN OTHERS THEN DBMS_OUTPUT.PUT_LINE('Fehler erwartet: ' || SQLERRM);
END;
/
-- Änderungen überprüfen (in Basistabelle oder View)
SELECT m.*, a.name FROM mitarbeiter m LEFT JOIN abteilung a ON m.abtnr=a.abtnr WHERE m.mnr = 1001;
SELECT * FROM emp_dept_name_v WHERE mnr = 1001;

-- Aufräumen (optional)
-- DELETE FROM mitarbeiter WHERE mnr = 1001;
-- COMMIT; -- Wichtig, um Änderungen zu speichern!
```

---

**Beispiel 2: View mit formatierten Datumswerten (deine Anforderung)**

* **Ziel:** Einen View `emp_fmt_date_v` erstellen, der Datumswerte (z.B. `einstell_datum`) im Format `YYYY-MM-DD` anzeigt. Updates und Inserts über den View sollen möglich sein, wobei das formatierte Datum zurück in ein `DATE`-Format für die Basistabelle konvertiert wird.
* **Voraussetzungen:** Tabelle `MITARBEITER` mit einer Spalte `EINSTELL_DATUM` vom Typ `DATE`.
* **1. View erstellen:**

```sql
CREATE OR REPLACE VIEW emp_fmt_date_v AS
SELECT
    mnr, nachname, job, gehalt, abtnr,
    TO_CHAR(einstell_datum, 'YYYY-MM-DD') AS einstell_datum_fmt
FROM
    mitarbeiter;

-- Diese View ist auf 'einstell_datum_fmt' nicht direkt aktualisierbar.
```

* **2. INSTEAD OF Trigger erstellen:**

```sql
CREATE OR REPLACE TRIGGER trg_emp_fmt_date_v_iud -- IUD = Insert/Update/Delete
   INSTEAD OF INSERT OR UPDATE OR DELETE ON emp_fmt_date_v
   FOR EACH ROW
DECLARE
   v_einstell_datum DATE; -- Variable für das konvertierte Datum
BEGIN
   -- Behandlung von INSERT
   IF INSERTING THEN
      -- Konvertiere das formatierte Datum aus :NEW zurück in DATE
      BEGIN
         v_einstell_datum := TO_DATE(:NEW.einstell_datum_fmt, 'YYYY-MM-DD');
      EXCEPTION
         WHEN OTHERS THEN -- Fängt z.B. Formatfehler ab
            RAISE_APPLICATION_ERROR(-20003, 'Ungültiges Datumsformat für einstell_datum_fmt: "' || :NEW.einstell_datum_fmt || '". Bitte Format YYYY-MM-DD verwenden.');
      END;

      -- Füge in die Basistabelle ein
      INSERT INTO mitarbeiter (mnr, nachname, job, gehalt, abtnr, einstell_datum)
      VALUES (:NEW.mnr, :NEW.nachname, :NEW.job, :NEW.gehalt, :NEW.abtnr, v_einstell_datum);

   -- Behandlung von UPDATE
   ELSIF UPDATING THEN
      -- Wurde versucht, das formatierte Datum zu ändern?
      -- Wir prüfen, ob sich der Wert geändert hat oder ob die Spalte im SET-Teil vorkam.
      -- Da wir UPDATING('view_spalte') nicht direkt haben, prüfen wir auf Wertänderung.
      IF :OLD.einstell_datum_fmt <> :NEW.einstell_datum_fmt
         OR (:OLD.einstell_datum_fmt IS NULL AND :NEW.einstell_datum_fmt IS NOT NULL)
         OR (:OLD.einstell_datum_fmt IS NOT NULL AND :NEW.einstell_datum_fmt IS NULL)
      THEN
         -- Ja, Datum wurde geändert -> konvertiere neues Datum
         BEGIN
            v_einstell_datum := TO_DATE(:NEW.einstell_datum_fmt, 'YYYY-MM-DD');
         EXCEPTION
            WHEN OTHERS THEN
               RAISE_APPLICATION_ERROR(-20003, 'Ungültiges Datumsformat für einstell_datum_fmt: "' || :NEW.einstell_datum_fmt || '". Bitte Format YYYY-MM-DD verwenden.');
         END;
      ELSE
         -- Nein, formatiertes Datum wurde nicht geändert. Wir müssen das Original-DATE beibehalten.
         -- Sicherste Methode: Originaldatum aus der Basistabelle lesen.
         -- (Alternative Annahme: Wenn String gleich, ist DATE gleich - riskant bei Formatänderungen)
          SELECT einstell_datum INTO v_einstell_datum FROM mitarbeiter WHERE mnr = :OLD.mnr;
      END IF;

      -- Update die Basistabelle
      UPDATE mitarbeiter
         SET nachname       = :NEW.nachname,
             job            = :NEW.job,
             gehalt         = :NEW.gehalt,
             abtnr          = :NEW.abtnr,
             einstell_datum = v_einstell_datum -- Setze das ermittelte Datum
       WHERE mnr = :OLD.mnr; -- Identifiziere Zeile über PK

   -- Behandlung von DELETE
   ELSIF DELETING THEN
      -- Lösche aus der Basistabelle
      DELETE FROM mitarbeiter
      WHERE mnr = :OLD.mnr; -- Identifiziere Zeile über PK

   END IF;

EXCEPTION
    WHEN OTHERS THEN
        -- Logge ggf. den Fehler
        RAISE; -- Leite den Fehler weiter
END trg_emp_fmt_date_v_iud;
/
```

* **3. Demonstration:**

```sql
SET SERVEROUTPUT ON;
-- Erfolgreicher Insert
INSERT INTO emp_fmt_date_v (mnr, nachname, job, gehalt, abtnr, einstell_datum_fmt)
VALUES (1003, 'Schmidt', 'CLERK', 1600, 20, '2024-11-15');
-- Erfolgreiches Update (Datum ändern)
UPDATE emp_fmt_date_v SET einstell_datum_fmt = '2025-02-01' WHERE mnr = 1003;
-- Erfolgreiches Update (nur Gehalt ändern)
UPDATE emp_fmt_date_v SET gehalt = 1700 WHERE mnr = 1003;
-- Fehlgeschlagenes Update (falsches Format)
BEGIN
 UPDATE emp_fmt_date_v SET einstell_datum_fmt = '01/03/2025' WHERE mnr = 1003;
EXCEPTION WHEN OTHERS THEN DBMS_OUTPUT.PUT_LINE('Fehler erwartet: ' || SQLERRM);
END;
/
-- Erfolgreiches Löschen
DELETE FROM emp_fmt_date_v WHERE mnr = 1003;

-- Prüfen
SELECT * FROM mitarbeiter WHERE mnr = 1003;
-- COMMIT; -- Nicht vergessen!
```

---

**Zusammenfassung Schritt 11:**

* Du verstehst, dass `INSTEAD OF` Trigger verwendet werden, um DML-Operationen auf (ansonsten nicht-aktualisierbaren) Views zu ermöglichen.
* Sie feuern *anstelle* der DML auf dem View und definieren die Aktionen auf den Basistabellen.
* Sie arbeiten implizit auf Zeilenebene (`FOR EACH ROW`).
* `:OLD` und `:NEW` beziehen sich auf die Spalten des Views im Kontext der DML-Operation.
* Die Trigger-Logik muss die Übersetzung von View-Operationen zu Basistabellen-Operationen durchführen (z.B. Schlüssel-Lookup, Datentypkonvertierung).
* Du hast zwei konkrete Beispiele gemäß deinen Anforderungen implementiert gesehen.

---

# **Schritt 12: Trigger Teil 1.2 – Compound Trigger**

**Motivation: Warum Compound Trigger?**

1.  **Problem "Mutating Table" (ORA-04091):** Stell dir vor, du möchtest in einem `FOR EACH ROW`-Trigger auf der Tabelle `MITARBEITER` prüfen, ob das neue Gehalt eines Mitarbeiters mehr als 20% über dem *aktuellen Durchschnittsgehalt aller Mitarbeiter* liegt. Wenn du versuchst, `SELECT AVG(gehalt) FROM mitarbeiter` innerhalb dieses Row-Level-Triggers auszuführen, bekommst du einen `ORA-04091: table ... is mutating, trigger/function may not see it`-Fehler. Warum? Weil die Tabelle `MITARBEITER` sich gerade mitten in einer Änderungsoperation befindet (der Trigger feuert ja für jede Zeile), und Oracle kann keinen konsistenten Lesezugriff garantieren.
2.  **Performance:** Manchmal muss man vor oder nach der eigentlichen DML-Operation einmalige Setup- oder Aufräumarbeiten durchführen (z.B. Referenzdaten laden, globale Variablen initialisieren, Ergebnisse prüfen). Dies in einem Row-Level-Trigger für jede Zeile zu tun, wäre ineffizient.
3.  **Zustand über Zeitpunkte hinweg:** Es ist oft nützlich, Informationen zu sammeln, während die Zeilen verarbeitet werden (z.B. die IDs aller geänderten Zeilen), und diese gesammelten Informationen dann *nach* Abschluss aller Zeilenänderungen gesammelt zu verarbeiten.

**Die Lösung: Compound Trigger**

Ein Compound Trigger fasst die Logik für **mehrere Zeitpunkte** (Timing Points) innerhalb **eines einzigen Trigger-Objekts** zusammen. Er erlaubt es dir, Code für folgende Zeitpunkte zu definieren:

* `BEFORE STATEMENT`: Wird **einmal** ausgeführt, *bevor* das auslösende DML-Statement beginnt. Ideal für Initialisierungen.
* `BEFORE EACH ROW`: Wird für **jede betroffene Zeile** ausgeführt, *bevor* die Änderung angewendet wird. Zugriff auf `:OLD` und `:NEW` (bei `INSERT` nur `:NEW`). `:NEW` kann geändert werden.
* `AFTER EACH ROW`: Wird für **jede betroffene Zeile** ausgeführt, *nachdem* die Änderung angewendet wurde. Zugriff auf `:OLD` und `:NEW`. Ideal zum Sammeln von Informationen über die geänderten Zeilen.
* `AFTER STATEMENT`: Wird **einmal** ausgeführt, *nachdem* das gesamte DML-Statement abgeschlossen ist (und alle Row-Level-Trigger gefeuert haben). Die Tabelle ist jetzt wieder in einem stabilen Zustand und **kann sicher abgefragt werden** (löst das Mutating-Table-Problem). Ideal für abschließende Prüfungen, Bereinigungen oder das Verarbeiten gesammelter Daten.

**Struktur eines Compound Triggers:**

```sql
CREATE [OR REPLACE] TRIGGER trigger_name
  FOR {INSERT | UPDATE [OF spalte,...] | DELETE} ON tabelle_name
  COMPOUND TRIGGER -- Schlüsselwort!

  -- Optional: Globaler Deklarationsbereich
  -- Variablen, Typen, Konstanten, die in allen Timing-Abschnitten sichtbar sind
  TYPE ...
  g_variable TYP;
  g_collection TYP;

-- Jetzt folgen die Timing-Abschnitte (mindestens einer muss vorhanden sein)

  BEFORE STATEMENT IS
  BEGIN
    -- Code hier
  END BEFORE STATEMENT;

  BEFORE EACH ROW IS
  BEGIN
    -- Code hier (Zugriff auf :OLD, :NEW)
  END BEFORE EACH ROW;

  AFTER EACH ROW IS
  BEGIN
    -- Code hier (Zugriff auf :OLD, :NEW)
  END AFTER EACH ROW;

  AFTER STATEMENT IS
  BEGIN
    -- Code hier (kann Tabelle sicher abfragen)
  END AFTER STATEMENT;

END trigger_name;
/
```

**Wichtig:** Die globalen Variablen (im Deklarationsbereich direkt unter `COMPOUND TRIGGER`) behalten ihren Wert über die verschiedenen Timing Points hinweg *innerhalb der Ausführung eines einzelnen DML-Statements*. Das ist der Schlüssel, um Informationen z.B. im `AFTER EACH ROW` zu sammeln und im `AFTER STATEMENT` zu verwenden.

---

**Beispiel 1: Gehalt darf nicht > 20% über dem Durchschnitt aller Mitarbeiter sein**

* **Ziel:** Verhindern, dass ein `INSERT` oder `UPDATE` das Gehalt eines Mitarbeiters auf einen Wert setzt, der mehr als 20% über dem Durchschnittsgehalt *aller* Mitarbeiter liegt (nach der Änderung).
* **Lösung:** Wir sammeln die IDs und neuen Gehälter der betroffenen Mitarbeiter im `AFTER EACH ROW` und prüfen sie dann gesammelt gegen den Durchschnitt im `AFTER STATEMENT`.

```sql
CREATE OR REPLACE TRIGGER trg_emp_sal_avg_check
  FOR INSERT OR UPDATE OF gehalt ON mitarbeiter
  COMPOUND TRIGGER

  -- Globale Collection zum Sammeln der betroffenen Mitarbeiter
  TYPE t_emp_sal_rec IS RECORD (
    mnr mitarbeiter.mnr%TYPE,
    neues_gehalt mitarbeiter.gehalt%TYPE
  );
  TYPE t_emp_sal_tab IS TABLE OF t_emp_sal_rec INDEX BY PLS_INTEGER;
  g_betroffene_ma t_emp_sal_tab;

  -- Wird einmal vor dem Statement ausgeführt
  BEFORE STATEMENT IS
  BEGIN
     g_betroffene_ma.DELETE; -- Leere die Collection zu Beginn jedes Statements
  END BEFORE STATEMENT;

  -- Wird nach jeder geänderten/eingefügten Zeile ausgeführt
  AFTER EACH ROW IS
     l_index PLS_INTEGER;
  BEGIN
     l_index := g_betroffene_ma.COUNT + 1;
     g_betroffene_ma(l_index).mnr := :NEW.mnr;
     g_betroffene_ma(l_index).neues_gehalt := :NEW.gehalt;
  END AFTER EACH ROW;

  -- Wird einmal nach dem gesamten Statement ausgeführt
  AFTER STATEMENT IS
    v_avg_gehalt      mitarbeiter.gehalt%TYPE;
    v_max_erlaubt     mitarbeiter.gehalt%TYPE;
    v_ma_nachname     mitarbeiter.nachname%TYPE;
  BEGIN
     -- Wenn keine Zeilen betroffen waren (z.B. UPDATE mit WHERE-Klausel, die nichts findet),
     -- oder keine Gehälter betroffen waren, brauchen wir nichts zu tun.
     IF g_betroffene_ma.COUNT = 0 THEN
        RETURN;
     END IF;

     -- Berechne das aktuelle Durchschnittsgehalt (Tabelle ist jetzt stabil)
     SELECT AVG(gehalt) INTO v_avg_gehalt FROM mitarbeiter;

     -- Behandle Fall, dass Tabelle leer sein könnte (AVG gibt NULL zurück)
     IF v_avg_gehalt IS NULL THEN
        v_avg_gehalt := 0;
     END IF;

     v_max_erlaubt := v_avg_gehalt * 1.20;

     -- Prüfe jeden betroffenen Mitarbeiter
     FOR i IN 1 .. g_betroffene_ma.COUNT LOOP
        IF g_betroffene_ma(i).neues_gehalt > v_max_erlaubt THEN
           -- Hole den Namen für eine bessere Fehlermeldung (optional)
           BEGIN
              SELECT nachname INTO v_ma_nachname FROM mitarbeiter WHERE mnr = g_betroffene_ma(i).mnr;
           EXCEPTION WHEN NO_DATA_FOUND THEN v_ma_nachname := '?'; -- Sollte nicht passieren nach INSERT/UPDATE
           END;

           -- Fehler auslösen -> Das gesamte INSERT/UPDATE wird rückgängig gemacht!
           RAISE_APPLICATION_ERROR(-20101,
              'Gehaltsprüfung fehlgeschlagen für Mitarbeiter ' || v_ma_nachname || ' (MNR ' || g_betroffene_ma(i).mnr || '). ' ||
              'Neues Gehalt ' || g_betroffene_ma(i).neues_gehalt || ' übersteigt das Maximum von ' || ROUND(v_max_erlaubt, 2) ||
              ' (20% über dem Durchschnitt ' || ROUND(v_avg_gehalt,2) || ').');
        END IF;
     END LOOP;

  END AFTER STATEMENT;

END trg_emp_sal_avg_check;
/

-- Demonstration:
-- Angenommen Durchschnittsgehalt ist ca. 2000, max erlaubt wäre 2400
-- UPDATE mitarbeiter SET gehalt = 5000 WHERE mnr = 7788; -- Sollte Fehler -20101 auslösen
-- UPDATE mitarbeiter SET gehalt = 2300 WHERE mnr = 7788; -- Sollte funktionieren
-- COMMIT;
```

---

**Beispiel 2: Gehalt darf nicht > 20% über dem Durchschnitt seiner Abteilung sein**

* **Ziel:** Ähnlich wie oben, aber die Prüfung erfolgt gegen den Durchschnitt der *jeweiligen Abteilung* des Mitarbeiters.
* **Lösung:** Wir sammeln `mnr`, `neues_gehalt` und `abtnr`. Im `AFTER STATEMENT` iterieren wir durch die betroffenen Mitarbeiter und berechnen bei Bedarf den Abteilungsdurchschnitt.

```sql
CREATE OR REPLACE TRIGGER trg_emp_dept_sal_avg_check
  -- Muss auch auf Änderung von ABTNR reagieren, da dies den relevanten Durchschnitt ändert!
  FOR INSERT OR UPDATE OF gehalt, abtnr ON mitarbeiter
  COMPOUND TRIGGER

  -- Globale Collection zum Sammeln der betroffenen Mitarbeiter inkl. Abteilung
  TYPE t_emp_dept_sal_rec IS RECORD (
    mnr          mitarbeiter.mnr%TYPE,
    neues_gehalt mitarbeiter.gehalt%TYPE,
    abtnr        mitarbeiter.abtnr%TYPE
  );
  TYPE t_emp_dept_sal_tab IS TABLE OF t_emp_dept_sal_rec INDEX BY PLS_INTEGER;
  g_betroffene_ma t_emp_dept_sal_tab;

  -- Wird einmal vor dem Statement ausgeführt
  BEFORE STATEMENT IS
  BEGIN
     g_betroffene_ma.DELETE; -- Leere die Collection
  END BEFORE STATEMENT;

  -- Wird nach jeder geänderten/eingefügten Zeile ausgeführt
  AFTER EACH ROW IS
     l_index PLS_INTEGER;
  BEGIN
     l_index := g_betroffene_ma.COUNT + 1;
     g_betroffene_ma(l_index).mnr := :NEW.mnr;
     g_betroffene_ma(l_index).neues_gehalt := :NEW.gehalt;
     g_betroffene_ma(l_index).abtnr := :NEW.abtnr;
  END AFTER EACH ROW;

  -- Wird einmal nach dem gesamten Statement ausgeführt
  AFTER STATEMENT IS
    -- Hilfscollection, um Durchschnitt pro Abteilung nur einmal zu berechnen
    TYPE t_dept_avg_rec IS RECORD (
       avg_gehalt  mitarbeiter.gehalt%TYPE,
       max_erlaubt mitarbeiter.gehalt%TYPE
    );
    TYPE t_dept_avg_tab IS TABLE OF t_dept_avg_rec INDEX BY mitarbeiter.abtnr%TYPE;
    v_abt_durchschnitte t_dept_avg_tab;

    v_aktuelle_abtnr  mitarbeiter.abtnr%TYPE;
    v_ma_nachname     mitarbeiter.nachname%TYPE;

  BEGIN
     IF g_betroffene_ma.COUNT = 0 THEN RETURN; END IF;

     -- Gehe alle betroffenen Mitarbeiter durch
     FOR i IN 1 .. g_betroffene_ma.COUNT LOOP
        v_aktuelle_abtnr := g_betroffene_ma(i).abtnr;

        -- Nur prüfen, wenn Mitarbeiter einer Abteilung zugeordnet ist
        IF v_aktuelle_abtnr IS NOT NULL THEN

           -- Haben wir den Durchschnitt für diese Abteilung schon berechnet?
           IF NOT v_abt_durchschnitte.EXISTS(v_aktuelle_abtnr) THEN
              -- Nein: Berechne Durchschnitt und Limit für diese Abteilung
              SELECT NVL(AVG(gehalt), 0) INTO v_abt_durchschnitte(v_aktuelle_abtnr).avg_gehalt
                FROM mitarbeiter
               WHERE abtnr = v_aktuelle_abtnr;

              v_abt_durchschnitte(v_aktuelle_abtnr).max_erlaubt :=
                 v_abt_durchschnitte(v_aktuelle_abtnr).avg_gehalt * 1.20;
           END IF;

           -- Prüfe das Gehalt des aktuellen Mitarbeiters gegen das Abteilungs-Limit
           IF g_betroffene_ma(i).neues_gehalt > v_abt_durchschnitte(v_aktuelle_abtnr).max_erlaubt THEN
              SELECT nachname INTO v_ma_nachname FROM mitarbeiter WHERE mnr = g_betroffene_ma(i).mnr;
              RAISE_APPLICATION_ERROR(-20102,
                 'Gehaltsprüfung fehlgeschlagen für Mitarbeiter ' || v_ma_nachname || ' (MNR ' || g_betroffene_ma(i).mnr || ') in Abteilung ' || v_aktuelle_abtnr || '. ' ||
                 'Neues Gehalt ' || g_betroffene_ma(i).neues_gehalt || ' übersteigt das Abteilungs-Maximum von ' || ROUND(v_abt_durchschnitte(v_aktuelle_abtnr).max_erlaubt, 2) ||
                 ' (20% über dem Durchschnitt ' || ROUND(v_abt_durchschnitte(v_aktuelle_abtnr).avg_gehalt, 2) || ').');
           END IF;
        END IF; -- END IF v_aktuelle_abtnr IS NOT NULL
     END LOOP; -- END LOOP durch betroffene Mitarbeiter

  END AFTER STATEMENT;

END trg_emp_dept_sal_avg_check;
/
-- Demonstration:
-- UPDATE mitarbeiter SET gehalt = <wert>, [abtnr = <neue_abtnr>] WHERE mnr = <nr>;
-- Teste mit Werten innerhalb und außerhalb des Limits für die jeweilige Abteilung.
-- COMMIT;
```

---

**Beispiel 3: Beim Löschen des letzten Mitarbeiters einer Abteilung soll die Abteilung mitgelöscht werden**

* **Ziel:** Wenn ein `DELETE`-Statement den letzten Mitarbeiter aus einer Abteilung entfernt, soll automatisch auch der Eintrag dieser Abteilung aus der `ABTEILUNG`-Tabelle gelöscht werden.
* **Lösung:** Wir sammeln im `AFTER EACH ROW` die Abteilungsnummern der gelöschten Mitarbeiter (jede Nummer nur einmal). Im `AFTER STATEMENT` prüfen wir für jede dieser Abteilungen, ob noch Mitarbeiter vorhanden sind. Wenn nicht, löschen wir die Abteilung.

```sql
CREATE OR REPLACE TRIGGER trg_emp_dept_cleanup
  FOR DELETE ON mitarbeiter
  COMPOUND TRIGGER

  -- Globale Collection zum Sammeln der betroffenen Abteilungsnummern (eindeutig)
  TYPE t_dept_tab IS TABLE OF mitarbeiter.abtnr%TYPE INDEX BY PLS_INTEGER;
  g_betroffene_abtnr t_dept_tab;
  -- Alternativ: Associative Array für Eindeutigkeit nutzen
  TYPE t_dept_assoc_tab IS TABLE OF BOOLEAN INDEX BY mitarbeiter.abtnr%TYPE;
  g_betroffene_abtnr_assoc t_dept_assoc_tab;


  BEFORE STATEMENT IS
  BEGIN
     -- g_betroffene_abtnr.DELETE;
     g_betroffene_abtnr_assoc.DELETE;
  END BEFORE STATEMENT;

  AFTER EACH ROW IS
  BEGIN
     -- Speichere die Abteilungsnummer des gelöschten Mitarbeiters, falls vorhanden
     IF :OLD.abtnr IS NOT NULL THEN
        -- Stelle sicher, dass jede Nummer nur einmal vorkommt
        g_betroffene_abtnr_assoc(:OLD.abtnr) := TRUE;
     END IF;
  END AFTER EACH ROW;

  AFTER STATEMENT IS
    v_ma_anzahl    NUMBER;
    v_aktuelle_abtnr mitarbeiter.abtnr%TYPE;
  BEGIN
    -- Iteriere durch die gesammelten (eindeutigen) Abteilungsnummern
    v_aktuelle_abtnr := g_betroffene_abtnr_assoc.FIRST; -- Erster Index (AbtNr)
    WHILE v_aktuelle_abtnr IS NOT NULL LOOP

       -- Prüfe, ob noch Mitarbeiter in dieser Abteilung existieren (Tabelle ist stabil)
       SELECT COUNT(*)
         INTO v_ma_anzahl
         FROM mitarbeiter
        WHERE abtnr = v_aktuelle_abtnr;

       -- Wenn keine Mitarbeiter mehr da sind, lösche die Abteilung
       IF v_ma_anzahl = 0 THEN
          DELETE FROM abteilung WHERE abtnr = v_aktuelle_abtnr;
          -- Optional: Log-Meldung ausgeben
          DBMS_OUTPUT.PUT_LINE('Info: Abteilung ' || v_aktuelle_abtnr || ' wurde gelöscht (keine Mitarbeiter mehr).');
       END IF;

       v_aktuelle_abtnr := g_betroffene_abtnr_assoc.NEXT(v_aktuelle_abtnr); -- Nächster Index (AbtNr)
    END LOOP;

  END AFTER STATEMENT;

END trg_emp_dept_cleanup;
/
-- Demonstration:
-- Finde eine Abteilung mit nur einem Mitarbeiter (z.B. Abt 40, falls vorhanden)
-- SELECT abtnr, count(*) FROM mitarbeiter GROUP BY abtnr;
-- DELETE FROM mitarbeiter WHERE abtnr = <abtnr_mit_einem_ma>;
-- Prüfe danach: SELECT * FROM mitarbeiter WHERE abtnr = <abtnr_mit_einem_ma>; (sollte leer sein)
-- Prüfe danach: SELECT * FROM abteilung WHERE abtnr = <abtnr_mit_einem_ma>; (sollte auch leer sein)
-- COMMIT;
-- Teste auch das Löschen eines MA aus einer Abteilung mit mehreren Mitarbeitern -> Abteilung darf nicht gelöscht werden.
```

**Zusammenfassung Schritt 12:**

* Du verstehst, dass Compound Trigger mehrere Timing Points (`BEFORE/AFTER STATEMENT`, `BEFORE/AFTER EACH ROW`) in einem Trigger vereinen.
* Du kennst die Vorteile: Lösung für Mutating Tables, Performance-Optimierung, gemeinsamer Zustand über Timing Points.
* Du weißt, wie man globale Variablen/Collections im Trigger deklariert und nutzt, um Informationen zwischen den Timing Points (besonders von Row-Level zu `AFTER STATEMENT`) zu übergeben.
* Du hast gesehen, wie man die drei von dir genannten Anwendungsfälle mit Compound Triggern implementiert, insbesondere die Prüfungen gegen aggregierte Daten im `AFTER STATEMENT`-Teil.

 ---

# **Schritt 13: Datenstrukturen Teil 1 – Records**

**Was ist ein Record?**

Ein **Record** (Datensatz) ist ein zusammengesetzter Datentyp, der es dir erlaubt, mehrere logisch zusammengehörige Datenelemente (genannt **Felder** oder *fields*) unterschiedlicher Datentypen unter einem einzigen Namen zu gruppieren. Stell es dir wie eine Struktur (struct in C) oder ein einfaches Objekt vor, das verschiedene Attribute bündelt. Records sind extrem nützlich, um Daten als logische Einheit zu behandeln, z.B. alle Informationen über einen Kunden oder eine Adresse.

**Arten von Records:**

PL/SQL kennt hauptsächlich zwei Arten von Records:

1.  **Tabellen-basierte Records (`%ROWTYPE`):** Der Datentyp wird automatisch von der Struktur einer Datenbanktabelle oder eines Views abgeleitet.
2.  **Programmierer-definierte Records (`TYPE IS RECORD`):** Du definierst die Struktur (Felder und deren Typen) explizit selbst.

**1. Tabellen-basierte Records (`%ROWTYPE`)**

Dies ist der einfachste und oft praktischste Weg, eine Variable zu deklarieren, die eine komplette Zeile aus einer Tabelle oder einem View aufnehmen kann.

* **Syntax:**
    ```sql
    variablen_name tabellen_name%ROWTYPE;
    variablen_name view_name%ROWTYPE;
    ```
* **Vorteile:**
    * Sehr einfach zu deklarieren.
    * Die Struktur der Record-Variable passt sich **automatisch** an, wenn sich die Spalten der Tabelle/des Views ändern (z.B. Spalte hinzugefügt/entfernt, Datentyp geändert). Das macht deinen Code robuster!
    * Die Feldnamen im Record entsprechen exakt den Spaltennamen der Tabelle/des Views.
* **Typische Verwendung:** Zum Einlesen einer kompletten Zeile mit `SELECT * INTO` oder `Workspace cursor INTO`.

**Beispiel (`%ROWTYPE`):**

```sql
SET SERVEROUTPUT ON;

DECLARE
  -- Deklariere eine Record-Variable, die die Struktur der Tabelle MITARBEITER widerspiegelt
  v_mitarbeiter_zeile mitarbeiter%ROWTYPE;
BEGIN
  -- Lese die komplette Zeile für Mitarbeiter 7839 (KING) in die Record-Variable
  SELECT * -- Wählt alle Spalten der Tabelle
    INTO v_mitarbeiter_zeile
    FROM mitarbeiter
   WHERE mnr = 7839;

  -- Zugriff auf die Felder des Records über Punkt-Notation: variable.feldname (feldname = spaltenname)
  DBMS_OUTPUT.PUT_LINE('Mitarbeiter-Info (%ROWTYPE):');
  DBMS_OUTPUT.PUT_LINE('  MNR:       ' || v_mitarbeiter_zeile.mnr);
  DBMS_OUTPUT.PUT_LINE('  Nachname:  ' || v_mitarbeiter_zeile.nachname);
  DBMS_OUTPUT.PUT_LINE('  Job:       ' || v_mitarbeiter_zeile.job);
  DBMS_OUTPUT.PUT_LINE('  Gehalt:    ' || v_mitarbeiter_zeile.gehalt);
  DBMS_OUTPUT.PUT_LINE('  AbtNr:     ' || v_mitarbeiter_zeile.abtnr);
  DBMS_OUTPUT.PUT_LINE('  Einst.Dat: ' || TO_CHAR(v_mitarbeiter_zeile.einstell_datum, 'DD.MM.YYYY'));

  -- Du kannst Felder auch ändern (wenn es kein IN-Parameter ist)
  v_mitarbeiter_zeile.gehalt := v_mitarbeiter_zeile.gehalt + 100;
  DBMS_OUTPUT.PUT_LINE('  Neues Gehalt (nur in Variable): ' || v_mitarbeiter_zeile.gehalt);

  -- Wichtig: Die Änderung ist nur in der PL/SQL-Variable, nicht in der Datenbank!
  -- Um die DB zu ändern, bräuchte man ein UPDATE:
  -- UPDATE mitarbeiter SET ROW = v_mitarbeiter_zeile WHERE mnr = v_mitarbeiter_zeile.mnr;
  -- (UPDATE mit SET ROW ist eine spezielle Syntax, die mit %ROWTYPE gut funktioniert)

EXCEPTION
  WHEN NO_DATA_FOUND THEN
    DBMS_OUTPUT.PUT_LINE('Mitarbeiter nicht gefunden.');
  WHEN OTHERS THEN
    DBMS_OUTPUT.PUT_LINE('Fehler: ' || SQLERRM);
END;
/
```

**2. Programmierer-definierte Records (`TYPE IS RECORD`)**

Hier definierst du die Struktur des Records selbst. Das ist nützlich, wenn du Daten gruppieren möchtest, die nicht direkt einer einzelnen Datenbankzeile entsprechen.

* **Schritt 1: Typdefinition:** Zuerst definierst du einen neuen Record-*Typ* (eine Vorlage). Dies geschieht normalerweise im `DECLARE`-Teil oder (für Wiederverwendbarkeit) in einer Package-Spezifikation.
    ```sql
    TYPE typ_name IS RECORD (
      feldname1 datentyp1 [NOT NULL] [:= standardwert1],
      feldname2 datentyp2 [NOT NULL] [DEFAULT standardwert2],
      ...
      feldnameN datentypN
    );
    ```
    * `feldname`: Der Name des Feldes innerhalb des Records.
    * `datentyp`: Jeder gültige PL/SQL-Datentyp, inklusive `%TYPE`, anderen Record-Typen oder Collection-Typen.
    * `NOT NULL`: Das Feld muss immer einen Wert haben.
    * `:=` oder `DEFAULT`: Optionaler Standardwert für das Feld.
* **Schritt 2: Variablendeklaration:** Dann deklarierst du eine Variable dieses neu definierten Typs.
    ```sql
    variablen_name typ_name;
    ```

**Beispiel (Programmierer-definierter Record):**

```sql
SET SERVEROUTPUT ON;

DECLARE
  -- Schritt 1: Definiere einen Record-Typ für eine Adresse
  TYPE t_adress_record IS RECORD (
     strasse      VARCHAR2(100),
     hausnummer   VARCHAR2(10),
     plz          VARCHAR2(10) NOT NULL, -- Muss angegeben werden
     ort          VARCHAR2(50) DEFAULT 'Wien', -- Standardwert
     land         VARCHAR2(50) := 'Österreich' -- Standardwert
  );

  -- Schritt 2: Deklariere Variablen dieses Typs
  v_lieferadresse   t_adress_record;
  v_rechnungsadresse t_adress_record;

BEGIN
  -- Werte zuweisen (Feld für Feld)
  v_lieferadresse.strasse    := 'Hauptstraße';
  v_lieferadresse.hausnummer := '42a';
  v_lieferadresse.plz        := '1010';
  -- v_lieferadresse.ort verwendet den Default 'Wien'
  -- v_lieferadresse.land verwendet den Default 'Österreich'

  DBMS_OUTPUT.PUT_LINE('Lieferadresse:');
  DBMS_OUTPUT.PUT_LINE('  Straße: ' || v_lieferadresse.strasse || ' ' || v_lieferadresse.hausnummer);
  DBMS_OUTPUT.PUT_LINE('  PLZ/Ort: ' || v_lieferadresse.plz || ' ' || v_lieferadresse.ort);
  DBMS_OUTPUT.PUT_LINE('  Land: ' || v_lieferadresse.land);

  -- Ganzen Record zuweisen (Kopie wird erstellt)
  v_rechnungsadresse := v_lieferadresse;

  -- Ändere etwas in der Kopie
  v_rechnungsadresse.ort := 'Linz';
  v_rechnungsadresse.plz := '4020';

  DBMS_OUTPUT.PUT_LINE('Rechnungsadresse (nach Änderung):');
  DBMS_OUTPUT.PUT_LINE('  PLZ/Ort: ' || v_rechnungsadresse.plz || ' ' || v_rechnungsadresse.ort);

  DBMS_OUTPUT.PUT_LINE('Lieferadresse (unverändert):');
  DBMS_OUTPUT.PUT_LINE('  PLZ/Ort: ' || v_lieferadresse.plz || ' ' || v_lieferadresse.ort);

END;
/
```

**Records als Parameter und Rückgabewerte**

Records sind sehr praktisch, um mehrere zusammengehörige Werte als eine Einheit an Prozeduren oder Funktionen zu übergeben oder von Funktionen zurückzugeben.

* **Voraussetzung:** Der Record-Typ muss sowohl dem aufrufenden als auch dem aufgerufenen Programm bekannt sein. Am besten definiert man wiederverwendbare Record-Typen daher in einer **Package-Spezifikation**.

**Beispiel (Record als Parameter – Typdefinition im Package):**

```sql
-- 1. Package Spezifikation mit Record-Typ und Prozedur-Deklaration
CREATE OR REPLACE PACKAGE geo_pkg AS

  -- Record-Typ für geografische Koordinaten
  TYPE t_koordinaten_rec IS RECORD (
     laengengrad  NUMBER,
     breitengrad  NUMBER
  );

  -- Prozedur, die einen Record dieses Typs entgegennimmt
  PROCEDURE zeige_koordinaten (p_titel IN VARCHAR2, p_coords IN t_koordinaten_rec);

END geo_pkg;
/

-- 2. Package Body mit Prozedur-Implementierung
CREATE OR REPLACE PACKAGE BODY geo_pkg AS

  PROCEDURE zeige_koordinaten (p_titel IN VARCHAR2, p_coords IN t_koordinaten_rec) IS
  BEGIN
     IF p_coords.laengengrad IS NULL OR p_coords.breitengrad IS NULL THEN
        DBMS_OUTPUT.PUT_LINE(p_titel || ': Koordinaten unvollständig.');
        RETURN;
     END IF;

     DBMS_OUTPUT.PUT_LINE(p_titel || ':');
     DBMS_OUTPUT.PUT_LINE('  Längengrad: ' || p_coords.laengengrad);
     DBMS_OUTPUT.PUT_LINE('  Breitengrad: ' || p_coords.breitengrad);
  END zeige_koordinaten;

END geo_pkg;
/

-- 3. Verwendung der Prozedur mit dem Record-Parameter
SET SERVEROUTPUT ON;

DECLARE
  -- Variable vom Typ deklarieren, der im Package definiert ist
  v_standort_linz  geo_pkg.t_koordinaten_rec;
  v_standort_wien  geo_pkg.t_koordinaten_rec;
BEGIN
  -- Record-Felder befüllen
  v_standort_linz.laengengrad := 14.286;
  v_standort_linz.breitengrad := 48.306;

  v_standort_wien.laengengrad := 16.373;
  -- v_standort_wien.breitengrad bleibt NULL

  -- Prozedur mit dem Record als Parameter aufrufen
  geo_pkg.zeige_koordinaten(p_titel => 'Standort Linz', p_coords => v_standort_linz);
  geo_pkg.zeige_koordinaten('Standort Wien', v_standort_wien); -- Positionsnotation auch möglich

END;
/
```

**Wichtige Eigenschaften von Records:**

* **Zuweisung:** Records können nur Records desselben Typs zugewiesen werden (`record1 := record2;`). Dabei werden alle Felder kopiert (Copy-Semantik).
* **Vergleich:** Records können **nicht** direkt auf Gleichheit verglichen werden (`IF record1 = record2 THEN...` ist ungültig!). Vergleiche müssen Feld für Feld erfolgen. Man kann aber auf `NULL` prüfen (`IF record1 IS NULL THEN...`, was aber selten sinnvoll ist, da der Record selbst nie `NULL` ist, nur seine Felder können `NULL` sein).
* **Initialisierung:** Felder sind standardmäßig `NULL`, es sei denn, ein Default-Wert wurde im `TYPE IS RECORD` definiert.

**Zusammenfassung Schritt 13:**

* Du kennst Records als Möglichkeit, zusammengehörige Daten zu gruppieren.
* Du kannst Records mit `%ROWTYPE` basierend auf Tabellen/Views deklarieren und nutzen.
* Du kannst eigene Record-Typen mit `TYPE IS RECORD` definieren und Variablen davon deklarieren.
* Du weißt, wie man auf Record-Felder zugreift (`record.feld`) und Werte zuweist (einzeln oder als Ganzes).
* Du verstehst, dass Records als Parameter (`IN`, `OUT`, `IN OUT`) und Rückgabewerte verwendet werden können, wobei der Typ idealerweise in einem Package definiert wird.

---

# **Schritt 14: Datenstrukturen Teil 2.1 – Collections (Nested Tables)**

**Was ist eine Collection?**

Eine Collection (Sammlung) in PL/SQL ist eine Variable, die **mehrere Elemente desselben Datentyps** speichern kann. Man kann sie sich wie einen Array oder eine Liste in anderen Programmiersprachen vorstellen. Sie sind extrem nützlich, um Mengen von Daten im Speicher zu verarbeiten.

PL/SQL kennt drei Arten von Collections:
1.  **Nested Tables (Verschachtelte Tabellen):** Dynamisch wachsende, geordnete Listen. Können (theoretisch) in Datenbankspalten gespeichert werden. Beginnen dicht (Index 1, 2, 3...), können aber durch Löschen von Elementen "löchrig" (sparse) werden. Der Index ist immer ein Integer (`PLS_INTEGER`).
2.  **Associative Arrays (Index-by-Tables):** Dynamisch wachsend, können von Anfang an löchrig sein. Der Index kann ein Integer (`PLS_INTEGER`) oder ein String (`VARCHAR2` etc.) sein. Können *nicht* direkt in DB-Spalten gespeichert werden. Sehr flexibel für temporäre Datenhaltung, Lookups, Caching in PL/SQL. *(Kommen wir vielleicht später noch dazu, wenn gewünscht)*.
3.  **Varrays (Variable-Size Arrays):** Haben eine feste *maximale* Größe, die bei der Typdefinition festgelegt wird. Sind immer dicht (keine Lücken). Können in DB-Spalten gespeichert werden. Weniger flexibel als Nested Tables.

Wir konzentrieren uns jetzt auf **Nested Tables**.

**Arbeiten mit Nested Tables**

1.  **Typdefinition:** Ähnlich wie bei Records definierst du zuerst einen Collection-*Typ*.
    ```sql
    TYPE collection_typ_name IS TABLE OF element_datentyp [NOT NULL];
    ```
    * `element_datentyp`: Der Datentyp der Elemente in der Collection (z.B. `NUMBER`, `VARCHAR2(100)`, `mitarbeiter%ROWTYPE`, `geo_pkg.t_koordinaten_rec`, etc.).
    * `NOT NULL`: Wenn hier angegeben, darf kein `NULL`-Wert in die Collection eingefügt werden.
    * Ort: `DECLARE`-Teil oder (besser für Wiederverwendung) Package-Spezifikation.

2.  **Variablendeklaration:** Deklariere eine Variable des Collection-Typs.
    ```sql
    variablen_name collection_typ_name;
    ```

3.  **Initialisierung (SEHR WICHTIG!):** Im Gegensatz zu einfachen Variablen sind Collection-Variablen nach der Deklaration **atomar `NULL`**. Du kannst ihnen keine Elemente hinzufügen, bevor du sie nicht **initialisiert** hast. Dies geschieht mit einem **Konstruktor**, der denselben Namen wie der Collection-Typ hat.
    ```sql
    -- Initialisiert eine leere Collection
    variablen_name := collection_typ_name();

    -- Initialisiert eine Collection mit Anfangswerten
    variablen_name := collection_typ_name(wert1, wert2, wert3, ...);
    -- Beispiel mit Records:
    -- variablen_name := collection_typ_name(record_instanz1, record_instanz2, ...);
    ```

4.  **Elemente hinzufügen (`EXTEND`):** Nach der Initialisierung kannst du mit der `EXTEND`-Methode Platz für neue Elemente schaffen (am Ende der Collection).
    ```sql
    variablen_name.EXTEND;       -- Fügt Platz für EIN neues (NULL) Element am Ende hinzu.
    variablen_name.EXTEND(n);    -- Fügt Platz für 'n' neue (NULL) Elemente am Ende hinzu.
    variablen_name.EXTEND(n, i); -- Kopiert das Element am Index 'i' n-mal und fügt die Kopien am Ende an.

    -- Nach EXTEND muss dem neuen Element ein Wert zugewiesen werden:
    variablen_name.EXTEND;
    variablen_name(variablen_name.LAST) := neuer_wert; -- LAST gibt den Index des letzten Elements zurück
    ```

5.  **Elemente ansprechen:** Über den Index (1-basiert) in runden Klammern.
    ```sql
    -- Wert lesen
    v_wert := variablen_name(index);
    -- Wert schreiben
    variablen_name(index) := anderer_wert;
    ```
    **Achtung:** Greife nie auf einen Index zu, der nicht existiert! Prüfe vorher mit `EXISTS`.

6.  **Wichtige Methoden:** (Werden mit Punkt-Notation auf die Collection-Variable angewendet)
    * `COUNT`: (`NUMBER`) Anzahl der Elemente in der Collection.
    * `FIRST`: (`PLS_INTEGER`) Index des ersten Elements (meist 1). `NULL` bei leerer Collection.
    * `LAST`: (`PLS_INTEGER`) Index des letzten Elements. `NULL` bei leerer Collection.
    * `EXISTS(index)`: (`BOOLEAN`) Gibt `TRUE` zurück, wenn am `index` ein Element existiert, sonst `FALSE`. **Sehr wichtig!**
    * `DELETE`: Löscht Elemente.
        * `variablen_name.DELETE;` -> Löscht *alle* Elemente. Collection ist danach leer, aber noch initialisiert (nicht `NULL`).
        * `variablen_name.DELETE(index);` -> Löscht das Element am `index`. Macht die Collection ggf. löchrig (sparse).
        * `variablen_name.DELETE(start_idx, end_idx);` -> Löscht Elemente im Bereich.
    * `EXTEND`: Siehe Punkt 4.
    * `TRIM`: Entfernt *ein* Element vom *Ende*.
    * `TRIM(n)`: Entfernt `n` Elemente vom *Ende*.
    * `PRIOR(index)`: (`PLS_INTEGER`) Gibt den nächstniedrigeren existierenden Index zurück. `NULL`, wenn keiner existiert. Nützlich für Iteration.
    * `NEXT(index)`: (`PLS_INTEGER`) Gibt den nächsthöheren existierenden Index zurück. `NULL`, wenn keiner existiert. Nützlich für Iteration.

7.  **Iterieren (Schleifen):**
    * **Dichte Collections (Index 1..COUNT):**
        ```sql
        FOR i IN 1 .. variablen_name.COUNT LOOP
           -- Zugriff mit variablen_name(i)
        END LOOP;
        ```
    * **Potenziell löchrige (sparse) Collections (Sicherer):**
        ```sql
        FOR i IN variablen_name.FIRST .. variablen_name.LAST LOOP
           IF variablen_name.EXISTS(i) THEN
              -- Zugriff mit variablen_name(i)
           END IF;
        END LOOP;
        ```
    * **Flexibelste Methode (WHILE mit FIRST/NEXT):**
        ```sql
        DECLARE
          idx PLS_INTEGER := variablen_name.FIRST;
        BEGIN
          WHILE idx IS NOT NULL LOOP
             -- Zugriff mit variablen_name(idx)
             idx := variablen_name.NEXT(idx); -- Gehe zum nächsten existierenden Index
          END LOOP;
        END;
        ```

**Beispiel 1: Nested Table mit einfachen Werten (VARCHAR2)**

```sql
SET SERVEROUTPUT ON;

DECLARE
  -- Typdefinition
  TYPE t_teilnehmerliste IS TABLE OF VARCHAR2(100);

  -- Variable deklarieren
  v_kurs_teilnehmer t_teilnehmerliste;
BEGIN
  -- Initialisieren (!!!)
  v_kurs_teilnehmer := t_teilnehmerliste(); -- Leere Liste

  -- Elemente hinzufügen
  v_kurs_teilnehmer.EXTEND;
  v_kurs_teilnehmer(1) := 'Maier';
  v_kurs_teilnehmer.EXTEND;
  v_kurs_teilnehmer(v_kurs_teilnehmer.LAST) := 'Huber';
  v_kurs_teilnehmer.EXTEND(2); -- Platz für 2 weitere schaffen
  v_kurs_teilnehmer(3) := 'Schmidt';
  v_kurs_teilnehmer(4) := 'Wagner';

  DBMS_OUTPUT.PUT_LINE('Anzahl Teilnehmer: ' || v_kurs_teilnehmer.COUNT);
  DBMS_OUTPUT.PUT_LINE('Erster Teilnehmer: ' || v_kurs_teilnehmer(v_kurs_teilnehmer.FIRST));
  DBMS_OUTPUT.PUT_LINE('Letzter Teilnehmer: ' || v_kurs_teilnehmer(v_kurs_teilnehmer.LAST));

  -- Element ändern
  v_kurs_teilnehmer(2) := 'Huber-Mayer';

  -- Element löschen (macht die Liste löchrig/sparse)
  DBMS_OUTPUT.PUT_LINE('Lösche Element an Index 3 (Schmidt)...');
  v_kurs_teilnehmer.DELETE(3);

  DBMS_OUTPUT.PUT_LINE('Aktuelle Teilnehmer (mit WHILE-Loop):');
  DECLARE
    idx PLS_INTEGER := v_kurs_teilnehmer.FIRST;
  BEGIN
    WHILE idx IS NOT NULL LOOP
       DBMS_OUTPUT.PUT_LINE('  Index ' || idx || ': ' || v_kurs_teilnehmer(idx));
       idx := v_kurs_teilnehmer.NEXT(idx);
    END LOOP;
  END;

  -- Existenz prüfen
  IF v_kurs_teilnehmer.EXISTS(3) THEN
     DBMS_OUTPUT.PUT_LINE('Element 3 existiert.');
  ELSE
     DBMS_OUTPUT.PUT_LINE('Element 3 existiert NICHT mehr.');
  END IF;

  -- Alle löschen
  -- v_kurs_teilnehmer.DELETE;
  -- DBMS_OUTPUT.PUT_LINE('Anzahl nach DELETE: ' || v_kurs_teilnehmer.COUNT);

END;
/
```

**Beispiel 2: Nested Table mit Records**

```sql
SET SERVEROUTPUT ON;

DECLARE
  -- Record-Typ (könnte auch aus Package kommen)
  TYPE t_produkt_rec IS RECORD (
     prod_id   VARCHAR2(10),
     name      VARCHAR2(100),
     preis     NUMBER(10,2)
  );
  -- Collection-Typ für Records
  TYPE t_warenkorb IS TABLE OF t_produkt_rec;

  -- Variable
  v_mein_warenkorb t_warenkorb;
  v_produkt        t_produkt_rec; -- Hilfsvariable zum Befüllen
BEGIN
  -- Initialisieren
  v_mein_warenkorb := t_warenkorb();

  -- Produkt 1 hinzufügen
  v_mein_warenkorb.EXTEND;
  v_produkt.prod_id := 'A-100';
  v_produkt.name    := 'Apfel';
  v_produkt.preis   := 0.50;
  v_mein_warenkorb(v_mein_warenkorb.LAST) := v_produkt; -- Ganzen Record zuweisen

  -- Produkt 2 hinzufügen
  v_mein_warenkorb.EXTEND;
  v_produkt.prod_id := 'B-250';
  v_produkt.name    := 'Banane';
  v_produkt.preis   := 0.75;
  v_mein_warenkorb(v_mein_warenkorb.LAST) := v_produkt;

  DBMS_OUTPUT.PUT_LINE('Warenkorb (' || v_mein_warenkorb.COUNT || ' Artikel):');
  FOR i IN v_mein_warenkorb.FIRST .. v_mein_warenkorb.LAST LOOP
     IF v_mein_warenkorb.EXISTS(i) THEN
        DBMS_OUTPUT.PUT_LINE('  - ' || v_mein_warenkorb(i).name ||
                             ' (ID: ' || v_mein_warenkorb(i).prod_id ||
                             ', Preis: ' || v_mein_warenkorb(i).preis || ')');
     END IF;
  END LOOP;

  -- Gesamtwert berechnen
  DECLARE
     v_gesamt NUMBER := 0;
     idx PLS_INTEGER := v_mein_warenkorb.FIRST;
  BEGIN
     WHILE idx IS NOT NULL LOOP
        v_gesamt := v_gesamt + v_mein_warenkorb(idx).preis;
        idx := v_mein_warenkorb.NEXT(idx);
     END LOOP;
     DBMS_OUTPUT.PUT_LINE('Gesamtwert: ' || v_gesamt);
  END;

END;
/
```

**Collections als Parameter und Rückgabewerte**

Genau wie Records können auch Collections an Prozeduren/Funktionen übergeben oder von Funktionen zurückgegeben werden. Der Collection-Typ muss wieder bekannt sein (ideal: Definition im Package).

**Effizientes Befüllen aus SQL: `BULK COLLECT INTO`**

Wenn du das Ergebnis einer Abfrage in eine Collection laden willst, ist es **sehr ineffizient**, dies Zeile für Zeile mit einem Cursor-Loop zu tun. Verwende stattdessen `BULK COLLECT INTO`:

```sql
-- Annahme: t_mitarbeiter_nr_liste IS TABLE OF mitarbeiter.mnr%TYPE; ist definiert
DECLARE
  v_mnr_liste t_mitarbeiter_nr_liste;
BEGIN
  SELECT mnr -- Wähle Spalte(n), die zum Elementtyp der Collection passen
  BULK COLLECT INTO v_mnr_liste -- Lade alle Ergebnisse auf einmal
  FROM mitarbeiter
  WHERE abtnr = 10;

  -- Jetzt ist v_mnr_liste mit allen MNRs der Abteilung 10 gefüllt
  -- ... Verarbeitung der Liste ...
END;
/
```

**Zusammenfassung Schritt 14:**

* Du kennst Nested Tables als eine Art von dynamisch wachsenden Listen in PL/SQL.
* Du weißt, dass sie **initialisiert** werden müssen (`:= collection_typ()`).
* Du kannst Elemente hinzufügen (`EXTEND`), lesen/schreiben (`variable(index)`) und löschen (`DELETE`).
* Du kennst wichtige Methoden wie `COUNT`, `FIRST`, `LAST`, `EXISTS`, `NEXT`, `PRIOR`.
* Du kannst sicher durch (potenziell löchrige) Nested Tables iterieren.
* Du hast gesehen, wie man Collections von Records verwendet.
* Du kennst das Konzept von `BULK COLLECT INTO` für effizientes Befüllen aus SQL.
* Nested Tables können als Parameter übergeben und zurückgegeben werden (Typdefinition im Package empfohlen).

---

# **Schritt 15: Packages Teil 2 – Sichtbarkeit, Lebensdauer & Initialisierung**

Wir wissen bereits, dass Packages aus einer Spezifikation (öffentlich) und einem Body (Implementierung + privat) bestehen. Das hat direkte Auswirkungen darauf, was wo sichtbar ist und wie lange Variablen ihren Zustand behalten.

**1. Sichtbarkeit (Public vs. Private)**

* **Public Elemente (Deklaration in der Package Spezifikation):**
    * Variablen, Konstanten, Exceptions, Cursor, **Typen** (Records, Collections etc.) sowie die **Signaturen** (Köpfe) von Prozeduren und Funktionen, die in der Spezifikation deklariert sind, sind **öffentlich**.
    * Sie können von überall her angesprochen werden, wo der Benutzer die nötigen Rechte auf das Package hat (normalerweise `EXECUTE`-Recht). Der Zugriff erfolgt über `package_name.element_name`.
    * **Wichtig für Typen:** Damit du Variablen eines Record- oder Collection-Typs *außerhalb* des Packages deklarieren kannst (z.B. um sie als Parameter an eine öffentliche Prozedur zu übergeben), muss der Typ **in der Spezifikation** deklariert sein.

* **Private Elemente (Deklaration nur im Package Body):**
    * Alle Elemente (Variablen, Konstanten, Typen, Exceptions, Cursor, *komplette* Prozeduren und Funktionen), die **nur** im Package Body deklariert sind (und nicht in der Spezifikation), sind **privat**.
    * Sie sind **nur innerhalb desselben Package Bodys** sichtbar und aufrufbar. Von außen kann man nicht direkt darauf zugreifen.
    * **Zweck:** Kapselung von Implementierungsdetails, Hilfsroutinen, internen Zustandsvariablen, die nicht Teil der öffentlichen Schnittstelle sein sollen.

**2. Lebensdauer von Package-Variablen (Package State)**

Dies ist eine der mächtigsten Eigenschaften von Packages:

* **Initialisierung:** Wenn zum **ersten Mal** in einer Datenbank-Session auf irgendein Element eines Packages zugegriffen wird (egal ob Prozedur, Funktion oder Variable), wird das Package für diese Session **instanziiert** und in den Speicher (UGA - User Global Area) geladen. Dabei passiert Folgendes:
    * Alle im Package (egal ob Spec oder Body) deklarierten Variablen und Konstanten werden gemäß ihrer Deklaration initialisiert (`DEFAULT` oder `:=`).
    * Ein optionaler **Initialisierungsblock** im Package Body (siehe unten) wird ausgeführt.
* **Persistenz:** Nach der Initialisierung behalten alle Package-Variablen (public und private) ihren Zustand **für die gesamte Dauer der Datenbank-Session** bei. Sie verhalten sich wie globale Variablen auf Session-Ebene.
    * Beispiel: Wenn eine Prozedur im Package eine Package-Variable ändert, sieht ein späterer Aufruf einer anderen Prozedur *in derselben Session* den geänderten Wert.
    * **Wichtig:** Verschiedene Datenbank-Sessions haben *ihren eigenen, unabhängigen* Zustand für Package-Variablen. Änderungen in Session A sind nicht direkt in Session B sichtbar.
* **Ende der Lebensdauer:** Der Zustand geht verloren, wenn die Datenbank-Session beendet wird (Benutzer trennt Verbindung, Timeout etc.).

**3. Package Initialisierungsblock (Optional)**

Du kannst am Ende des Package **Bodys** einen optionalen Codeblock einfügen, der nur einmal pro Session ausgeführt wird, nämlich genau dann, wenn das Package zum ersten Mal geladen/initialisiert wird.

* **Syntax:** Ein `BEGIN ... EXCEPTION ... END;` Block direkt vor dem abschließenden `END package_name;` des **Package Bodys**.
* **Zweck:** Für komplexere Initialisierungsaufgaben, die über einfache Wertzuweisungen hinausgehen, z.B.:
    * Laden von Konfigurationsdaten aus Tabellen in Package-Variablen (z.B. Collections).
    * Prüfen von Systemvoraussetzungen.
    * Registrieren des Packages bei einem Framework.

**Beispiel: Demonstration von Sichtbarkeit und Zustand**

Wir erstellen ein Package, das Zähler verwaltet und Konfiguration "lädt".

```sql
-- Package Spezifikation (Öffentliche Schnittstelle)
CREATE OR REPLACE PACKAGE session_state_pkg AS

  -- Öffentliche Variable (kann von außen gelesen UND geschrieben werden)
  g_public_zaehler NUMBER := 0;

  -- Öffentliche Exception
  e_konfig_fehler EXCEPTION;

  -- Öffentlicher Record-Typ
  TYPE t_konfig_rec IS RECORD (
     parameter VARCHAR2(50),
     wert      VARCHAR2(200)
  );
  -- Öffentlicher Collection-Typ
  TYPE t_konfig_tab IS TABLE OF t_konfig_rec INDEX BY PLS_INTEGER;

  -- Öffentliche Prozedur zum Erhöhen der Zähler
  PROCEDURE erhoehe_zaehler;

  -- Öffentliche Funktion zum Lesen des (internen) privaten Zählers
  FUNCTION get_privaten_zaehler RETURN NUMBER;

  -- Öffentliche Funktion zum Holen der (intern gecachten) Konfiguration
  FUNCTION get_konfiguration RETURN t_konfig_tab;

END session_state_pkg;
/

-- Package Body (Implementierung & Private Elemente)
CREATE OR REPLACE PACKAGE BODY session_state_pkg AS

  -- Private Variable (nur im Body sichtbar/änderbar)
  g_private_zaehler NUMBER := 100;

  -- Private Konstante
  c_max_privat_zaehler CONSTANT NUMBER := 110;

  -- Private Collection für Konfigurations-Cache
  g_konfig_cache t_konfig_tab; -- Ist initial NULL!

  -- Private Hilfsprozedur (nur im Body aufrufbar)
  PROCEDURE lade_konfiguration IS
    v_dummy_config t_konfig_rec;
  BEGIN
     DBMS_OUTPUT.PUT_LINE('--- Private Prozedur lade_konfiguration wird ausgeführt ---');
     -- Simuliert das Laden aus einer Tabelle
     g_konfig_cache(1).parameter := 'TIMEOUT';
     g_konfig_cache(1).wert      := '300';
     g_konfig_cache(2).parameter := 'FEATURE_X_AKTIV';
     g_konfig_cache(2).wert      := 'TRUE';
     -- (Hier könnte z.B. ein Fehler auftreten, der e_konfig_fehler auslöst)
  EXCEPTION
     WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('!! Fehler beim Laden der Konfiguration !!');
        -- Lösche ggf. teilweise geladene Daten
        g_konfig_cache.DELETE;
        RAISE e_konfig_fehler; -- Leite die öffentliche Exception weiter
  END lade_konfiguration;

  -- Implementierung der öffentlichen Prozedur
  PROCEDURE erhoehe_zaehler IS
  BEGIN
     g_public_zaehler := g_public_zaehler + 1;

     IF g_private_zaehler < c_max_privat_zaehler THEN
        g_private_zaehler := g_private_zaehler + 1;
     ELSE
        DBMS_OUTPUT.PUT_LINE('INFO: Privater Zähler hat Maximum (' || c_max_privat_zaehler || ') erreicht.');
     END IF;
     DBMS_OUTPUT.PUT_LINE('> Zähler erhöht.');
  END erhoehe_zaehler;

  -- Implementierung der öffentlichen Funktion
  FUNCTION get_privaten_zaehler RETURN NUMBER IS
  BEGIN
     RETURN g_private_zaehler;
  END get_privaten_zaehler;

  -- Implementierung der öffentlichen Funktion
  FUNCTION get_konfiguration RETURN t_konfig_tab IS
  BEGIN
     -- Gibt die (hoffentlich im Initialisierungsblock) geladene Konfiguration zurück
     RETURN g_konfig_cache;
  END get_konfiguration;


-- *** Package Initialisierungsblock ***
BEGIN
   DBMS_OUTPUT.PUT_LINE('*** Initialisierung von session_state_pkg für Session ID: ' || SYS_CONTEXT('USERENV', 'SID') || ' ***');
   -- Rufe die private Prozedur auf, um den Cache zu füllen
   lade_konfiguration;
   -- Überschreibe ggf. den Startwert einer öffentlichen Variable
   g_public_zaehler := 1; -- Startet immer bei 1 nach Initialisierung
   DBMS_OUTPUT.PUT_LINE('*** Initialisierung abgeschlossen ***');

-- Fehlerbehandlung für den Initialisierungsblock selbst
EXCEPTION
   WHEN OTHERS THEN
       DBMS_OUTPUT.PUT_LINE('!!! FATAL: Fehler bei der Package-Initialisierung: ' || SQLERRM || ' !!!');
       -- Hier könnte man noch versuchen, einen sicheren Zustand herzustellen oder den Fehler loggen.
       RAISE; -- Fehler weiterleiten, damit der Aufrufer ihn bemerkt
END session_state_pkg; -- Ende des Package Bodys (inkl. Initialisierungsblock)
/
```

**Demonstration:**

```sql
SET SERVEROUTPUT ON SIZE UNLIMITED;

-- 1. Erster Zugriff -> Löst Initialisierung aus
DECLARE
  v_config session_state_pkg.t_konfig_tab;
  v_pub    NUMBER;
  v_priv   NUMBER;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Block 1: Erster Zugriff ---');
  -- Zugriff auf Funktion löst Initialisierung aus
  v_config := session_state_pkg.get_konfiguration();
  DBMS_OUTPUT.PUT_LINE('Konfig-Element 1: ' || v_config(1).parameter || '=' || v_config(1).wert);

  -- Zugriff auf Variablen nach Initialisierung
  v_pub  := session_state_pkg.g_public_zaehler;       -- Public Variable direkt
  v_priv := session_state_pkg.get_privaten_zaehler(); -- Private Variable über Funktion
  DBMS_OUTPUT.PUT_LINE('Initial Public Zähler: ' || v_pub);
  DBMS_OUTPUT.PUT_LINE('Initial Privat Zähler: ' || v_priv);
END;
/

-- 2. Zweiter Zugriff -> Keine erneute Initialisierung
DECLARE
  v_pub    NUMBER;
  v_priv   NUMBER;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Block 2: Zweiter Zugriff ---');
  session_state_pkg.erhoehe_zaehler(); -- Ruft Prozedur auf

  v_pub  := session_state_pkg.g_public_zaehler;
  v_priv := session_state_pkg.get_privaten_zaehler();
  DBMS_OUTPUT.PUT_LINE('Nach Erhöhung Public Zähler: ' || v_pub);   -- Sollte 2 sein
  DBMS_OUTPUT.PUT_LINE('Nach Erhöhung Privat Zähler: ' || v_priv);  -- Sollte 101 sein
END;
/

-- 3. Dritter Zugriff -> Zustand bleibt erhalten
DECLARE
  v_pub    NUMBER;
  v_priv   NUMBER;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Block 3: Dritter Zugriff ---');
  session_state_pkg.erhoehe_zaehler(); -- Nochmals erhöhen

  v_pub  := session_state_pkg.g_public_zaehler;
  v_priv := session_state_pkg.get_privaten_zaehler();
  DBMS_OUTPUT.PUT_LINE('Nach 2. Erhöhung Public Zähler: ' || v_pub);  -- Sollte 3 sein
  DBMS_OUTPUT.PUT_LINE('Nach 2. Erhöhung Privat Zähler: ' || v_priv); -- Sollte 102 sein
END;
/

-- 4. Versuch, auf private Elemente zuzugreifen (führt zu Fehlern!)
DECLARE
  v_test NUMBER;
BEGIN
   DBMS_OUTPUT.PUT_LINE('--- Block 4: Fehlerversuche ---');
   -- v_test := session_state_pkg.g_private_zaehler; -- FEHLER: PLS-00302: component 'G_PRIVATE_ZAEHLER' must be declared
   -- session_state_pkg.lade_konfiguration;          -- FEHLER: PLS-00302: component 'LADE_KONFIGURATION' must be declared
   DBMS_OUTPUT.PUT_LINE('Die obigen Zeilen würden Kompilierfehler verursachen.');
END;
/

-- 5. Verwendung des öffentlichen Typs
DECLARE
   -- Variable vom Typ deklarieren, der im Package öffentlich ist
   mein_konfig_eintrag session_state_pkg.t_konfig_rec;
BEGIN
    DBMS_OUTPUT.PUT_LINE('--- Block 5: Verwendung des Public Typs ---');
    mein_konfig_eintrag.parameter := 'USER_PREF';
    mein_konfig_eintrag.wert      := 'DARK_MODE';
    DBMS_OUTPUT.PUT_LINE('Mein Konfig-Eintrag: ' || mein_konfig_eintrag.parameter);
END;
/
-- Wenn man jetzt die Verbindung trennt und neu verbindet, würde Block 1 wieder
-- die Initialisierungsmeldung ausgeben und die Zähler wären auf ihren Startwerten.
```

**Zusammenfassung Schritt 15:**

* Du verstehst den Unterschied zwischen **öffentlichen** (in Spec deklarierten) und **privaten** (nur im Body deklarierten) Package-Elementen.
* Du weißt, dass Package-Variablen ihren Zustand **pro Datenbank-Session** behalten (Session State).
* Du kennst den **Initialisierungsblock** am Ende des Package Bodys und seinen Zweck.
* Du verstehst, wie die Sichtbarkeit von **Typen** und **Exceptions** von ihrer Deklaration in Spec oder Body abhängt und wie man öffentliche Typen/Exceptions verwendet.

---

# **Schritt 16: Fehlerbehandlung Teil 1 – Grundlagen und System-Exceptions**

**Was ist eine Exception?**

Eine Exception (Ausnahme[situation]) ist ein Fehler oder eine unerwartete Bedingung, die während der Ausführung deines PL/SQL-Codes auftritt. Beispiele sind der Versuch, durch Null zu teilen, eine Zeichenkette in eine Zahl zu konvertieren, die keine ist, oder wenn ein `SELECT INTO` keine oder zu viele Zeilen findet.

**Warum Fehler behandeln?**

Ohne Fehlerbehandlung führt eine Exception zum sofortigen Abbruch des PL/SQL-Blocks und oft zu einer kryptischen Fehlermeldung für den Benutzer oder das aufrufende Programm. Eine gute Fehlerbehandlung ermöglicht es dir:

* **Programmabstürze zu verhindern:** Dein Code kann kontrolliert auf Fehler reagieren.
* **Sinnvolle Aktionen auszuführen:** Fehler protokollieren, Standardwerte zurückgeben, alternative Pfade ausführen, dem Benutzer eine verständliche Meldung geben.
* **Transaktionssicherheit zu gewährleisten:** Sicherstellen, dass eine fehlgeschlagene Operation keine inkonsistenten Daten hinterlässt (meist durch Rollback der Transaktion).

**Der `EXCEPTION`-Block**

Der Kern der Fehlerbehandlung in PL/SQL ist der `EXCEPTION`-Block, der am Ende eines PL/SQL-Blocks (anonymer Block, Prozedur, Funktion, Trigger) stehen kann:

```sql
DECLARE
  -- Deklarationen
BEGIN
  -- Normaler Code, hier können Exceptions auftreten
  -- ...
EXCEPTION -- Beginn des Fehlerbehandlungsblocks
  WHEN exception_name_1 THEN
     -- Code, der ausgeführt wird, wenn exception_name_1 auftritt
     -- ...
  WHEN exception_name_2 OR exception_name_3 THEN
     -- Code, der ausgeführt wird, wenn exception_name_2 ODER exception_name_3 auftritt
     -- ...
  WHEN OTHERS THEN -- Optional, fängt alle nicht explizit genannten Fehler ab
     -- Code für alle anderen Exceptions
     -- Wichtig für Logging und generische Fehlerbehandlung
     -- ...
END; -- Ende des PL/SQL-Blocks
```

**Ablauf:**

1.  Der Code im `BEGIN`-Teil wird ausgeführt.
2.  Tritt **kein** Fehler auf, wird der gesamte `EXCEPTION`-Block übersprungen.
3.  Tritt ein Fehler (eine Exception) auf:
    a.  Die normale Ausführung im `BEGIN`-Teil stoppt sofort.
    b.  Die Kontrolle geht an den `EXCEPTION`-Block über.
    c.  PL/SQL sucht nach einem `WHEN`-Handler, der auf die aufgetretene Exception passt.
    d.  Der erste passende Handler wird ausgeführt.
    e.  **Nach** Ausführung des Handler-Codes gilt die Exception als **behandelt**, und der PL/SQL-Block endet **normal** (es sei denn, im Handler wird mit `RAISE;` die Exception erneut ausgelöst).
    f.  Wird kein spezifischer Handler gefunden, wird der `WHEN OTHERS`-Handler ausgeführt (falls vorhanden).
    g.  Wird überhaupt kein passender Handler gefunden (weder spezifisch noch `WHEN OTHERS`), **propagiert** die Exception nach außen zum aufrufenden Block/Programm, wo der Prozess von neuem beginnt. Wird sie nirgends behandelt, bricht das Programm ab.

**Benannte System-Exceptions (Named System Exceptions)**

Oracle hat viele häufig auftretende Fehlersituationen vordefiniert und ihnen Namen gegeben. Diese sind global im `STANDARD`-Package deklariert, du kannst sie direkt in deinen `WHEN`-Klauseln verwenden, ohne sie selbst deklarieren zu müssen.

Einige wichtige Beispiele (viele davon kennst du implizit schon):

* **`NO_DATA_FOUND`** (SQLCODE +100, ORA-01403): Wird ausgelöst, wenn ein `SELECT INTO` keine Zeilen findet.
* **`TOO_MANY_ROWS`** (SQLCODE -1422, ORA-01422): Wird ausgelöst, wenn ein `SELECT INTO` mehr als eine Zeile findet.
* `ZERO_DIVIDE` (SQLCODE -1476, ORA-01476): Versuch der Division durch Null.
* `VALUE_ERROR` (SQLCODE -6502, ORA-06502): Allgemeiner Fehler bei Konvertierungen, Berechnungen oder Wertzuweisungen (z.B. String zu lang für Variable, ungültiges Zeichen bei Zahlkonvertierung).
* `INVALID_CURSOR` (SQLCODE -1001, ORA-01001): Ungültige Cursor-Operation (z.B. `Workspace` von geschlossenem Cursor).
* `CURSOR_ALREADY_OPEN` (SQLCODE -6511, ORA-06511): Versuch, einen bereits offenen Cursor zu öffnen.
* `DUP_VAL_ON_INDEX` (SQLCODE -1, ORA-00001): Verletzung einer Unique-Constraint-Bedingung bei `INSERT` oder `UPDATE`.
* `ACCESS_INTO_NULL` (SQLCODE -6530, ORA-06530): Versuch, auf Attribute eines `NULL`-Objekts zuzugreifen.
* `COLLECTION_IS_NULL` (SQLCODE -6531, ORA-06531): Versuch, Methoden (außer `EXISTS`) auf einer nicht initialisierten Collection anzuwenden.

**Der `WHEN OTHERS`-Handler**

Dieser spezielle Handler fängt *jede* Exception ab, die nicht durch einen vorherigen, spezifischen `WHEN`-Handler abgefangen wurde.

* **Nützliche Funktionen im Handler:**
    * `SQLCODE`: Gibt den numerischen Oracle-Fehlercode der aktuellen Exception zurück (z.B. -1422 für `TOO_MANY_ROWS`).
    * `SQLERRM`: Gibt die dazugehörige Fehlermeldung als String zurück.
* **`RAISE;`:** Wenn du `RAISE;` (ohne Exception-Namen dahinter) innerhalb eines Exception-Handlers verwendest, wird die **ursprüngliche Exception**, die gerade behandelt wird, erneut ausgelöst und an den aufrufenden Block weitergereicht. Das ist nützlich, wenn du einen Fehler zwar lokal behandeln (z.B. loggen) willst, aber der Aufrufer trotzdem über den Fehler informiert werden soll.
* **Warnung:** Vermeide `WHEN OTHERS THEN NULL;` oder `WHEN OTHERS THEN -- nix tun`. Das unterdrückt Fehler stillschweigend und macht die Fehlersuche extrem schwierig! Nutze `WHEN OTHERS` zum Protokollieren, für allgemeine Aufräumarbeiten und eventuell zum Weiterleiten (`RAISE;`) oder zum Auslösen einer allgemeineren, benutzerdefinierten Exception.

**Beispiel: Fehlerbehandlung bei Mitarbeiterabfrage**

Wir erweitern unsere Prozedur zum Holen von Mitarbeiterdetails um eine robuste Fehlerbehandlung.

```sql
-- Wir nutzen das Package vom letzten Schritt für das Logging
CREATE OR REPLACE PROCEDURE get_mitarbeiter_details_robust (
  p_mnr       IN  mitarbeiter.mnr%TYPE,
  p_nachname  OUT mitarbeiter.nachname%TYPE,
  p_gehalt    OUT mitarbeiter.gehalt%TYPE,
  p_job       OUT mitarbeiter.job%TYPE,
  p_erfolg    OUT BOOLEAN -- Gibt TRUE zurück, wenn erfolgreich, sonst FALSE
)
IS
  -- Lokale Variable, nur zur Demonstration von VALUE_ERROR
  v_kurzname VARCHAR2(5);
BEGIN
  -- Initialisierung der OUT-Parameter und des Erfolgsflags
  p_nachname := NULL;
  p_gehalt   := NULL;
  p_job      := NULL;
  p_erfolg   := TRUE; -- Annahme: Es klappt

  -- Der eigentliche Code, der Fehler verursachen kann
  SELECT nachname, gehalt, job
    INTO p_nachname, p_gehalt, p_job
    FROM mitarbeiter
   WHERE mnr = p_mnr;

   -- Beispiel für möglichen VALUE_ERROR (wenn Nachname länger als 5 Zeichen)
   -- v_kurzname := p_nachname; -- Diese Zeile würde bei langen Namen Fehler -6502 auslösen

   session_state_pkg.log_meldung('Mitarbeiter ' || p_nachname || ' erfolgreich geholt.', 'DEBUG');

EXCEPTION -- Beginn der Fehlerbehandlung
  WHEN NO_DATA_FOUND THEN
     session_state_pkg.log_meldung('Mitarbeiter mit MNR ' || p_mnr || ' nicht gefunden.', 'WARN');
     p_erfolg := FALSE;
     -- OUT-Parameter sind schon NULL

  WHEN TOO_MANY_ROWS THEN
     -- Sollte bei Suche nach MNR (PK) nicht passieren, aber als Beispiel
     session_state_pkg.log_meldung('Mehr als ein Mitarbeiter mit MNR ' || p_mnr || ' gefunden (unerwartet!).', 'ERROR');
     p_erfolg := FALSE;

  WHEN VALUE_ERROR THEN
     session_state_pkg.log_meldung('Wertfehler aufgetreten (z.B. String zu lang, Konvertierungsfehler). SQLCODE: ' || SQLCODE || ' - ' || SQLERRM, 'ERROR');
     p_erfolg := FALSE;

  WHEN OTHERS THEN -- Fängt alle anderen Fehler ab
     session_state_pkg.log_meldung('Unerwarteter Fehler in get_mitarbeiter_details_robust für MNR ' || p_mnr ||
                                   '. SQLCODE: ' || SQLCODE || ' - ' || SQLERRM, 'FATAL');
     p_erfolg := FALSE;
     -- Hier könnte man entscheiden, den Fehler weiterzuleiten:
     -- RAISE;
END get_mitarbeiter_details_robust;
/

-- Demonstration
SET SERVEROUTPUT ON SIZE UNLIMITED;
DECLARE
  v_name mitarbeiter.nachname%TYPE;
  v_sal  mitarbeiter.gehalt%TYPE;
  v_job  mitarbeiter.job%TYPE;
  v_ok   BOOLEAN;
BEGIN
  DBMS_OUTPUT.PUT_LINE('--- Test mit gültiger MNR (7839) ---');
  get_mitarbeiter_details_robust(7839, v_name, v_sal, v_job, v_ok);
  IF v_ok THEN
     DBMS_OUTPUT.PUT_LINE(' Erfolg! Name=' || v_name || ', Gehalt=' || v_sal || ', Job=' || v_job);
  ELSE
     DBMS_OUTPUT.PUT_LINE(' Fehlgeschlagen!');
  END IF;

  DBMS_OUTPUT.PUT_LINE('--- Test mit ungültiger MNR (9999) ---');
  get_mitarbeiter_details_robust(9999, v_name, v_sal, v_job, v_ok);
  IF v_ok THEN
     DBMS_OUTPUT.PUT_LINE(' Erfolg! Name=' || v_name || ', Gehalt=' || v_sal || ', Job=' || v_job);
  ELSE
     DBMS_OUTPUT.PUT_LINE(' Fehlgeschlagen! (Erwartet: NO_DATA_FOUND)');
  END IF;

  -- Hier könnte man noch einen Fehler provozieren, der von WHEN OTHERS gefangen wird,
  -- z.B. durch einen ungültigen Tabellennamen im SELECT (wenn man die Proc ändert).
END;
/
```

**Zusammenfassung Schritt 16:**

* Du verstehst das Konzept von Exceptions und die Notwendigkeit der Fehlerbehandlung.
* Du kennst die Struktur des `EXCEPTION`-Blocks mit `WHEN`-Klauseln.
* Du weißt, wie die Ausführung bei einer Exception zum `EXCEPTION`-Block springt und wie Exceptions behandelt oder propagiert werden.
* Du kennst wichtige benannte System-Exceptions wie `NO_DATA_FOUND`, `TOO_MANY_ROWS`, `VALUE_ERROR` etc. und weißt, wie man sie mit `WHEN exception_name THEN ...` fängt.
* Du verstehst den Zweck des `WHEN OTHERS`-Handlers und die Verwendung von `SQLCODE`, `SQLERRM` und `RAISE;`.

 ---

# **Schritt 17: Fehlerbehandlung Teil 2 – Benutzerdefinierte Exceptions**

Oft reichen die eingebauten System-Exceptions nicht aus, um spezifische Geschäftsregeln oder Anwendungsfehler darzustellen (z.B. "Kunde hat keinen gültigen Status", "Lagerbestand nicht ausreichend", "Gehaltslimit überschritten"). Hierfür gibt es benutzerdefinierte Exceptions. Es gibt zwei primäre Methoden:

1.  **Benannte benutzerdefinierte Exceptions:** Du deklarierst eine Variable vom Typ `EXCEPTION` und löst sie bei Bedarf mit `RAISE` aus.
2.  **Unbenannte Exceptions mit `RAISE_APPLICATION_ERROR`:** Du rufst eine eingebaute Prozedur auf, um sofort einen Fehler mit einer benutzerdefinierten Nummer und Meldung auszulösen.

**1. Benannte benutzerdefinierte Exceptions**

* **Schritt 1: Deklaration:** Deklariere eine Variable vom speziellen Typ `EXCEPTION`. Dies geschieht im `DECLARE`-Teil eines Blocks, einer Prozedur/Funktion oder (für globale Sichtbarkeit) in einer Package-Spezifikation.
    ```sql
    -- Syntax
    exception_variable_name EXCEPTION;

    -- Beispiele
    e_saldo_zu_gering      EXCEPTION;
    e_ungueltiger_status   EXCEPTION;
    ```
* **Schritt 2: Auslösen (`RAISE`):** Wenn die Fehlerbedingung in deinem Code eintritt, löst du die Exception mit dem `RAISE`-Befehl aus.
    ```sql
    DECLARE
      v_aktueller_saldo NUMBER := 100;
      v_abbuchung       NUMBER := 150;
      e_saldo_zu_gering EXCEPTION; -- Lokal deklariert
    BEGIN
      IF v_abbuchung > v_aktueller_saldo THEN
         RAISE e_saldo_zu_gering; -- Löse die benannte Exception aus
      END IF;
      -- ... Code für erfolgreiche Abbuchung ...
    EXCEPTION
       WHEN e_saldo_zu_gering THEN
          DBMS_OUTPUT.PUT_LINE('Fehler: Saldo nicht ausreichend!');
          -- Fehler hier behandelt
    END;
    /
    ```
* **Schritt 3: Behandeln (`WHEN`):** Im `EXCEPTION`-Block fängst du die Exception mit einer `WHEN`-Klausel, die den Namen deiner Exception-Variable verwendet.
* **Sichtbarkeit/Gültigkeit:**
    * Lokal deklarierte Exceptions (im `DECLARE` eines Blocks/Subprogramms) können nur innerhalb dieses Blocks und seiner inneren Blöcke ausgelöst und behandelt werden.
    * In einer **Package-Spezifikation** deklarierte Exceptions sind **öffentlich** und können im Package ausgelöst und von Code *außerhalb* des Packages behandelt werden (mit `WHEN package_name.exception_name THEN ...`). Das ist der übliche Weg für wiederverwendbare Anwendungsfehler.

**2. Unbenannte Exceptions mit `RAISE_APPLICATION_ERROR`**

Diese Methode ist oft einfacher, wenn du nur schnell einen Fehler mit einer spezifischen Meldung und Nummer signalisieren willst, ohne vorher eine Exception-Variable zu deklarieren.

* **Syntax:**
    ```sql
    RAISE_APPLICATION_ERROR ( error_number, message [, keep_error_stack_flag ] );
    ```
    * `error_number`: Eine **negative Ganzzahl zwischen -20000 und -20999**. Dieser Bereich ist für benutzerdefinierte Fehler reserviert. Wähle eine eindeutige Nummer für jeden Fehlertyp in deiner Anwendung.
    * `message`: Ein String (bis 2048 Bytes) mit deiner Fehlermeldung.
    * `keep_error_stack_flag` (Optional, `BOOLEAN`, Standard `FALSE`): `TRUE` fügt den Fehler zum bestehenden Fehlerstack hinzu, `FALSE` ersetzt ihn. Meist ist `FALSE` ausreichend.
* **Auslösen:** Direkt im Code aufrufen, wenn der Fehler auftritt.
    ```sql
    DECLARE
      v_status VARCHAR2(10) := 'GESPERRT';
    BEGIN
       IF v_status <> 'AKTIV' THEN
          RAISE_APPLICATION_ERROR(-20001, 'Aktion nicht erlaubt für Status: ' || v_status);
       END IF;
       -- ...
    END;
    / -- Führt zum Abbruch mit "ORA-20001: Aktion nicht erlaubt für Status: GESPERRT"
    ```
* **Behandeln:** Fehler, die mit `RAISE_APPLICATION_ERROR` ausgelöst werden, haben keinen eigenen Namen. Sie werden typischerweise im aufrufenden Block von einem `WHEN OTHERS`-Handler gefangen. Innerhalb von `WHEN OTHERS` kannst du dann `SQLCODE` prüfen (er enthält die `-20xxx`-Nummer) oder `SQLERRM` (enthält deine Meldung), um spezifischer zu reagieren oder zu loggen.

**Vergleich:**

* **Benannte Exceptions:** Besser für "bekannte" Anwendungsfehler, die möglicherweise an verschiedenen Stellen spezifisch behandelt werden müssen oder über mehrere Aufrufebenen propagiert werden sollen. Klarere Semantik im Code (`RAISE e_saldo_zu_gering;` ist oft lesbarer). Erfordern Deklaration (idealerweise zentral im Package).
* **`RAISE_APPLICATION_ERROR`:** Einfacher für "einmalige" Fehlerbedingungen. Nummer und Meldung sind direkt beim Auslösen definiert. Keine separate Deklaration nötig. Die `-20xxx`-Nummer kann von externen Tools oder im `WHEN OTHERS`-Handler zur Identifikation genutzt werden.

**Beispiel: Kombination beider Methoden in einem Package**

Wir erweitern das `order_pkg` von vorhin.

```sql
-- 1. Package Spezifikation (mit benannten Exceptions)
CREATE OR REPLACE PACKAGE order_pkg AS
   -- Benannte Exceptions für spezifische, bekannte Probleme
   e_insufficient_stock    EXCEPTION;
   e_product_discontinued  EXCEPTION; -- Neu

   -- Prozedur zur Bestellverarbeitung
   PROCEDURE process_order (
      p_customer_id   IN kunden.kunden_id%TYPE, -- Annahme: Tabelle kunden
      p_product_id    IN produkte.prod_id%TYPE, -- Annahme: Tabelle produkte
      p_quantity      IN NUMBER
   );
END order_pkg;
/

-- 2. Package Body
CREATE OR REPLACE PACKAGE BODY order_pkg AS

   -- Dummy-Funktionen für Demo
   FUNCTION get_stock_level (p_prod_id IN VARCHAR2) RETURN NUMBER IS
   BEGIN
      IF p_prod_id = 'PROD-X' THEN RETURN 5; ELSE RETURN 100; END IF;
   END;
   FUNCTION get_product_status (p_prod_id IN VARCHAR2) RETURN VARCHAR2 IS
   BEGIN
       IF p_prod_id = 'PROD-OLD' THEN RETURN 'DISCONTINUED'; ELSE RETURN 'ACTIVE'; END IF;
   END;
   FUNCTION get_customer_status (p_cust_id IN NUMBER) RETURN VARCHAR2 IS
   BEGIN
       IF p_cust_id = 99 THEN RETURN 'INACTIVE'; ELSIF p_cust_id = 1 THEN RETURN 'ACTIVE'; ELSE RETURN 'UNKNOWN'; END IF;
   END;

   -- Implementierung der Prozedur
   PROCEDURE process_order (
      p_customer_id   IN kunden.kunden_id%TYPE,
      p_product_id    IN produkte.prod_id%TYPE,
      p_quantity      IN NUMBER
   ) IS
     v_stock         NUMBER;
     v_prod_status   VARCHAR2(20);
     v_cust_status   VARCHAR2(20);
   BEGIN
     session_state_pkg.log_meldung('Starte Bestellverarbeitung: Kunde=' || p_customer_id || ', Produkt=' || p_product_id || ', Menge=' || p_quantity, 'INFO');

     -- **Prüfung 1: Kundenstatus (mit RAISE_APPLICATION_ERROR für "unerwartete" Probleme)**
     v_cust_status := get_customer_status(p_customer_id);
     IF v_cust_status = 'INACTIVE' THEN
        RAISE_APPLICATION_ERROR(-20050, 'Kunde ' || p_customer_id || ' ist inaktiv.');
     ELSIF v_cust_status = 'UNKNOWN' THEN
        RAISE_APPLICATION_ERROR(-20051, 'Kundenstatus für Kunde ' || p_customer_id || ' unbekannt.');
     END IF; -- Aktiver Kunde ist OK

     -- **Prüfung 2: Produktstatus (mit benannter Exception)**
     v_prod_status := get_product_status(p_product_id);
     IF v_prod_status = 'DISCONTINUED' THEN
        RAISE e_product_discontinued; -- Löse benannte Exception aus
     END IF;

     -- **Prüfung 3: Lagerbestand (mit benannter Exception)**
     v_stock := get_stock_level(p_product_id);
     IF p_quantity <= 0 THEN
        RAISE_APPLICATION_ERROR(-20055, 'Ungültige Menge: ' || p_quantity); -- Noch ein Beispiel für R_A_E
     END IF;
     IF p_quantity > v_stock THEN
        RAISE e_insufficient_stock; -- Löse benannte Exception aus
     END IF;

     -- **Wenn alle Prüfungen OK:**
     session_state_pkg.log_meldung('Alle Prüfungen bestanden. Führe Bestellung durch...', 'DEBUG');
     -- Hier käme jetzt die eigentliche Logik:
     -- UPDATE produkte SET lagerbestand = lagerbestand - p_quantity WHERE prod_id = p_product_id;
     -- INSERT INTO bestellungen(...) VALUES (...);
     session_state_pkg.log_meldung('Bestellung erfolgreich verarbeitet.', 'INFO');

   EXCEPTION
      -- Spezifische Behandlung für bekannte, benannte Fehler
      WHEN e_insufficient_stock THEN
         session_state_pkg.log_meldung('Bestellfehler: Nicht genügend Lagerbestand für Produkt ' || p_product_id || '. Benötigt: ' || p_quantity || ', Verfügbar: ' || v_stock, 'ERROR');
         -- Fehler wird hier behandelt (nur geloggt), nicht weitergeleitet

      WHEN e_product_discontinued THEN
         session_state_pkg.log_meldung('Bestellfehler: Produkt ' || p_product_id || ' ist nicht mehr verfügbar.', 'WARN');
         -- Fehler wird hier behandelt, nicht weitergeleitet
         -- Man könnte hier RAISE_APPLICATION_ERROR verwenden, um es doch zu signalisieren:
         -- RAISE_APPLICATION_ERROR(-20052, 'Produkt ' || p_product_id || ' ist nicht mehr verfügbar.');

      -- Kein spezifischer Handler für die Fehler -20050, -20051, -20055
      -- Diese werden (falls sie auftreten) an den Aufrufer weitergeleitet.

      -- WHEN OTHERS im Package Body ist oft nicht sinnvoll, wenn Fehler an den Aufrufer gehen sollen.
      -- Falls man hier doch einen WHEN OTHERS hätte:
      /*
      WHEN OTHERS THEN
         session_state_pkg.log_meldung('Unerwarteter Fehler in process_order: '||SQLCODE||' - '||SQLERRM, 'FATAL');
         RAISE; -- Unerwartete Fehler immer weiterleiten!
      */
   END process_order;

END order_pkg;
/

-- Demonstration im aufrufenden Block
SET SERVEROUTPUT ON SIZE UNLIMITED;
DECLARE
  PROCEDURE call_process_order(p_cust NUMBER, p_prod VARCHAR2, p_qty NUMBER) IS
  BEGIN
     DBMS_OUTPUT.PUT_LINE('--- Versuch: Kunde=' || p_cust || ', Produkt=' || p_prod || ', Menge=' || p_qty || ' ---');
     order_pkg.process_order(p_cust, p_prod, p_qty);
     DBMS_OUTPUT.PUT_LINE('--- Versuch erfolgreich abgeschlossen ---');
  EXCEPTION
     -- Fangen von Exceptions, die von der Prozedur NICHT intern behandelt wurden
     WHEN order_pkg.e_insufficient_stock OR order_pkg.e_product_discontinued THEN
        -- Diese sollten eigentlich nicht hier ankommen, da sie intern behandelt werden
        DBMS_OUTPUT.PUT_LINE('! Fehler (unerwartet hier): Benannte Exception im Block gefangen: ' || SQLERRM);
     WHEN OTHERS THEN
        -- Hier erwarten wir die Fehler von RAISE_APPLICATION_ERROR (-20050, -20051, -20055)
        IF SQLCODE BETWEEN -20000 AND -20999 THEN
           DBMS_OUTPUT.PUT_LINE('! Anwendungsfehler gefangen: ' || SQLCODE || ' - ' || SQLERRM);
        ELSE
           DBMS_OUTPUT.PUT_LINE('! Anderer unerwarteter Fehler gefangen: ' || SQLCODE || ' - ' || SQLERRM);
        END IF;
  END call_process_order;
BEGIN
  call_process_order(1, 'PROD-X', 3);     -- OK
  call_process_order(1, 'PROD-X', 10);    -- Fehler: e_insufficient_stock (intern behandelt)
  call_process_order(1, 'PROD-OLD', 1);   -- Fehler: e_product_discontinued (intern behandelt)
  call_process_order(99, 'PROD-X', 1);    -- Fehler: -20050 (extern gefangen)
  call_process_order(123, 'PROD-X', 1);   -- Fehler: -20051 (extern gefangen)
  call_process_order(1, 'PROD-X', 0);     -- Fehler: -20055 (extern gefangen)
END;
/
```

**Zusammenfassung Schritt 17:**

* Du kannst eigene Fehlerbedingungen mit **benannten Exceptions** (`exception_name EXCEPTION;` und `RAISE exception_name;`) definieren, auslösen und spezifisch behandeln (`WHEN exception_name THEN ...`).
* Du kannst alternativ die Prozedur **`RAISE_APPLICATION_ERROR(-20xxx, 'message');`** verwenden, um sofort einen Fehler mit Nummer und Meldung auszulösen, der typischerweise von `WHEN OTHERS` gefangen wird.
* In Packages deklarierte Exceptions/Typen sind ein mächtiges Werkzeug für saubere Schnittstellen und Fehlerbehandlung über Modulgrenzen hinweg.

---

# **Schritt 18: Fehlerbehandlung Teil 3 – Exceptions benennen mit `PRAGMA EXCEPTION_INIT`**

Wir haben gesehen, dass `RAISE_APPLICATION_ERROR(-20xxx, 'meldung')` sehr praktisch ist, um Fehler zu signalisieren. Der Nachteil ist, dass diese Fehler standardmäßig keinen Namen haben und daher im aufrufenden Code nur mit `WHEN OTHERS` gefangen werden können. Wenn man aber spezifisch auf einen bestimmten `-20xxx`-Fehler reagieren möchte (z.B. anders auf "Kunde unbekannt" als auf "Kunde inaktiv"), wird es mit `WHEN OTHERS` und `IF SQLCODE = -20xxx ... ELSIF SQLCODE = -20yyy ...` schnell unübersichtlich.

Hier kommt `PRAGMA EXCEPTION_INIT` ins Spiel.

**Was ist `PRAGMA EXCEPTION_INIT`?**

* Ein **Pragma** ist eine Anweisung an den PL/SQL-Compiler (kein Laufzeitbefehl).
* `PRAGMA EXCEPTION_INIT` weist den Compiler an, eine **vorher deklarierte benannte Exception-Variable** fest mit einer **spezifischen Oracle-Fehlernummer** zu verknüpfen.

**Syntax:**

Das Pragma muss im selben Deklarationsbereich (z.B. `DECLARE`, Package Spec, Package Body) stehen wie die Exception-Deklaration, und zwar *nach* dieser.

```sql
DECLARE
  -- 1. Benannte Exception deklarieren
  exception_name EXCEPTION;
  -- 2. Pragma, um die Exception mit einer Fehlernummer zu verbinden
  PRAGMA EXCEPTION_INIT (exception_name, oracle_fehler_nummer);
BEGIN
  -- ... Code ...
EXCEPTION
  WHEN exception_name THEN -- Fängt jetzt implizit Fehler mit 'oracle_fehler_nummer'
     -- ... Spezifische Behandlung ...
END;
```

* `exception_name`: Der Name der zuvor deklarierten Exception-Variable.
* `oracle_fehler_nummer`: Die Oracle-Fehlernummer (eine Ganzzahl). Dies können Standard-Oracle-Fehler sein (z.B. `-1` für Unique Constraint Violation, `-2292` für Foreign Key Violation bei `DELETE`) oder eben unsere benutzerdefinierten Nummern aus dem Bereich `-20000` bis `-20999`.
    *(Hinweis: +100 / `NO_DATA_FOUND` kann nicht verwendet werden).*

**Wie funktioniert das?**

Das Pragma sagt dem Compiler: "Immer wenn der Oracle-Fehler `oracle_fehler_nummer` auftritt, soll das so behandelt werden, als wäre die benannte Exception `exception_name` mit `RAISE exception_name;` ausgelöst worden."

**Vorteile:**

* Ermöglicht das **spezifische Fangen** von Fehlern, die mit `RAISE_APPLICATION_ERROR` ausgelöst wurden, über einen `WHEN exception_name THEN`-Handler.
* Erhöht die **Lesbarkeit und Struktur** des Exception-Handling-Codes, da man nicht mehr auf `IF SQLCODE = ...` im `WHEN OTHERS`-Block angewiesen ist.
* Man kann Standard-Oracle-Fehlern, die keinen vordefinierten Namen haben, einen eigenen, sprechenden Namen geben.

**Verwendung mit Packages (Best Practice):**

Der häufigste und sinnvollste Ansatz ist:

1.  **Deklariere** die benannte Exception in der **Package Spezifikation**.
2.  **Verknüpfe** sie mit der Fehlernummer mithilfe von `PRAGMA EXCEPTION_INIT` **direkt darunter in der Package Spezifikation**.
3.  Löse den Fehler im Package Body (oder wo auch immer der Fehler auftritt) entweder mit `RAISE package_name.exception_name;` ODER mit `RAISE_APPLICATION_ERROR(verknüpfte_nummer, 'meldung');` aus.
4.  **Fange** den Fehler im aufrufenden Code **immer** mit `WHEN package_name.exception_name THEN ...`. Dank des Pragmas funktioniert das unabhängig davon, ob im Package `RAISE` oder `RAISE_APPLICATION_ERROR` verwendet wurde.

**Beispiel: `order_pkg` mit `PRAGMA EXCEPTION_INIT`**

Wir passen das `order_pkg` so an, dass die Fehler `-20050`, `-20051` und `-20055` nun benannt gefangen werden können.

```sql
-- 1. Angepasste Package Spezifikation
CREATE OR REPLACE PACKAGE order_pkg AS
   -- Bestehende benannte Exceptions
   e_insufficient_stock    EXCEPTION;
   e_product_discontinued  EXCEPTION;

   -- NEU: Benannte Exceptions für RAISE_APPLICATION_ERROR-Nummern
   e_customer_inactive       EXCEPTION;
   PRAGMA EXCEPTION_INIT(e_customer_inactive, -20050); -- Verknüpft Name mit Nummer

   e_customer_unknown        EXCEPTION;
   PRAGMA EXCEPTION_INIT(e_customer_unknown, -20051);  -- Verknüpft Name mit Nummer

   e_invalid_quantity        EXCEPTION;
   PRAGMA EXCEPTION_INIT(e_invalid_quantity, -20055); -- Verknüpft Name mit Nummer

   -- Prozedur-Signatur bleibt gleich
   PROCEDURE process_order (
      p_customer_id   IN kunden.kunden_id%TYPE,
      p_product_id    IN produkte.prod_id%TYPE,
      p_quantity      IN NUMBER
   );
END order_pkg;
/

-- 2. Package Body (Hier KEINE Änderung nötig!)
-- Die RAISE_APPLICATION_ERROR Aufrufe mit -20050, -20051, -20055 funktionieren weiterhin.
-- Dank des Pragmas im Spec werden sie nach außen hin als e_customer_inactive etc. signalisiert.
-- (Der Body aus Schritt 17 kann unverändert bleiben)
CREATE OR REPLACE PACKAGE BODY order_pkg AS
   -- Dummy-Funktionen für Demo
   FUNCTION get_stock_level (p_prod_id IN VARCHAR2) RETURN NUMBER IS
   BEGIN
      IF p_prod_id = 'PROD-X' THEN RETURN 5; ELSE RETURN 100; END IF;
   END;
   FUNCTION get_product_status (p_prod_id IN VARCHAR2) RETURN VARCHAR2 IS
   BEGIN
       IF p_prod_id = 'PROD-OLD' THEN RETURN 'DISCONTINUED'; ELSE RETURN 'ACTIVE'; END IF;
   END;
   FUNCTION get_customer_status (p_cust_id IN NUMBER) RETURN VARCHAR2 IS
   BEGIN
       IF p_cust_id = 99 THEN RETURN 'INACTIVE'; ELSIF p_cust_id = 1 THEN RETURN 'ACTIVE'; ELSE RETURN 'UNKNOWN'; END IF;
   END;

   PROCEDURE process_order (
      p_customer_id   IN kunden.kunden_id%TYPE,
      p_product_id    IN produkte.prod_id%TYPE,
      p_quantity      IN NUMBER
   ) IS
     v_stock         NUMBER;
     v_prod_status   VARCHAR2(20);
     v_cust_status   VARCHAR2(20);
   BEGIN
     session_state_pkg.log_meldung('Starte Bestellverarbeitung: Kunde=' || p_customer_id || ', Produkt=' || p_product_id || ', Menge=' || p_quantity, 'INFO');
     v_cust_status := get_customer_status(p_customer_id);
     IF v_cust_status = 'INACTIVE' THEN
        RAISE_APPLICATION_ERROR(-20050, 'Kunde ' || p_customer_id || ' ist inaktiv.'); -- Bleibt
     ELSIF v_cust_status = 'UNKNOWN' THEN
        RAISE_APPLICATION_ERROR(-20051, 'Kundenstatus für Kunde ' || p_customer_id || ' unbekannt.'); -- Bleibt
     END IF;
     v_prod_status := get_product_status(p_product_id);
     IF v_prod_status = 'DISCONTINUED' THEN
        RAISE e_product_discontinued;
     END IF;
     v_stock := get_stock_level(p_product_id);
     IF p_quantity <= 0 THEN
        RAISE_APPLICATION_ERROR(-20055, 'Ungültige Menge: ' || p_quantity); -- Bleibt
     END IF;
     IF p_quantity > v_stock THEN
        RAISE e_insufficient_stock;
     END IF;
     session_state_pkg.log_meldung('Alle Prüfungen bestanden. Führe Bestellung durch...', 'DEBUG');
     session_state_pkg.log_meldung('Bestellung erfolgreich verarbeitet.', 'INFO');
   EXCEPTION
      WHEN e_insufficient_stock THEN
         session_state_pkg.log_meldung('Bestellfehler: Nicht genügend Lagerbestand für Produkt ' || p_product_id || '. Benötigt: ' || p_quantity || ', Verfügbar: ' || v_stock, 'ERROR');
      WHEN e_product_discontinued THEN
         session_state_pkg.log_meldung('Bestellfehler: Produkt ' || p_product_id || ' ist nicht mehr verfügbar.', 'WARN');
   END process_order;
END order_pkg;
/


-- 3. Angepasster aufrufender Block (kann jetzt spezifisch fangen)
SET SERVEROUTPUT ON SIZE UNLIMITED;
DECLARE
  PROCEDURE call_process_order(p_cust NUMBER, p_prod VARCHAR2, p_qty NUMBER) IS
  BEGIN
     DBMS_OUTPUT.PUT_LINE('--- Versuch: Kunde=' || p_cust || ', Produkt=' || p_prod || ', Menge=' || p_qty || ' ---');
     order_pkg.process_order(p_cust, p_prod, p_qty);
     DBMS_OUTPUT.PUT_LINE('--- Versuch erfolgreich abgeschlossen ---');
  EXCEPTION
     -- NEU: Spezifische Handler für die via PRAGMA benannten Fehler
     WHEN order_pkg.e_customer_inactive THEN
        DBMS_OUTPUT.PUT_LINE('! Fehler gefangen (spezifisch via Pragma): Kunde ' || p_cust || ' ist inaktiv.');
     WHEN order_pkg.e_customer_unknown THEN
        DBMS_OUTPUT.PUT_LINE('! Fehler gefangen (spezifisch via Pragma): Kundenstatus für ' || p_cust || ' ist unbekannt.');
     WHEN order_pkg.e_invalid_quantity THEN
        DBMS_OUTPUT.PUT_LINE('! Fehler gefangen (spezifisch via Pragma): Menge ' || p_qty || ' ist ungültig.');

     -- Diese kommen immer noch nicht hier an, da intern behandelt
     WHEN order_pkg.e_insufficient_stock OR order_pkg.e_product_discontinued THEN
        DBMS_OUTPUT.PUT_LINE('! Fehler (unerwartet hier): ' || SQLERRM);

     WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('! Anderer unerwarteter Fehler gefangen: ' || SQLCODE || ' - ' || SQLERRM);
  END call_process_order;
BEGIN
  call_process_order(1, 'PROD-X', 3);     -- OK
  call_process_order(1, 'PROD-X', 10);    -- Intern behandelt -> OK hier
  call_process_order(1, 'PROD-OLD', 1);   -- Intern behandelt -> OK hier
  call_process_order(99, 'PROD-X', 1);    -- Fehler: -20050 -> Gefangen von WHEN order_pkg.e_customer_inactive
  call_process_order(123, 'PROD-X', 1);   -- Fehler: -20051 -> Gefangen von WHEN order_pkg.e_customer_unknown
  call_process_order(1, 'PROD-X', 0);     -- Fehler: -20055 -> Gefangen von WHEN order_pkg.e_invalid_quantity
END;
/
```

**Zusammenfassung Schritt 18:**

* Du weißt, dass `PRAGMA EXCEPTION_INIT(exception_name, error_number)` eine benannte Exception mit einer Oracle-Fehlernummer verknüpft.
* Dies ermöglicht es, Fehler, die mit `RAISE_APPLICATION_ERROR(error_number, ...)` ausgelöst werden, über einen spezifischen `WHEN exception_name THEN`-Handler zu fangen.
* Die Deklaration der Exception und das Pragma erfolgen typischerweise zusammen in einer Package Spezifikation, um die Verknüpfung weithin bekannt zu machen.
* Dies verbessert die Struktur und Lesbarkeit der Fehlerbehandlung im aufrufenden Code.

Damit haben wir die verschiedenen Aspekte der Fehlerbehandlung aus deiner Liste (benannte System-, benannte benutzerdefinierte, unbenannte via R_A_E, Verknüpfung via Pragma, Deklaration im Package und Fangen im Aufrufer) abgedeckt.

---

Absolut! Kommen wir zum letzten großen Themenblock deiner Liste: **Native Dynamic SQL**.

**Schritt 19: Native Dynamic SQL Teil 1 – Grundlagen und `EXECUTE IMMEDIATE`**

**Was ist Dynamic SQL?**

Bisher haben wir hauptsächlich mit *statischem SQL* gearbeitet. Das bedeutet, der genaue Text deiner SQL-Anweisungen (`SELECT`, `INSERT`, `UPDATE`, `DELETE` etc.) oder deiner PL/SQL-Blöcke war fest im Code verankert und wurde vom Compiler zum Zeitpunkt der Kompilierung analysiert und validiert.

**Dynamic SQL** ist das Gegenteil: Hierbei werden SQL-Anweisungen oder PL/SQL-Blöcke erst zur **Laufzeit** als Zeichenketten (Strings) zusammengebaut und dann ausgeführt.

**Wann braucht man Dynamic SQL?**

* **Flexibilität:** Wenn Teile der Anweisung erst zur Laufzeit bekannt sind, z.B.:
    * Tabellen- oder Spaltennamen sind variabel (basierend auf Benutzereingaben oder Konfiguration). *Vorsicht: Design überdenken, oft gibt es bessere Lösungen.*
    * `WHERE`- oder `ORDER BY`-Klauseln ändern sich dynamisch.
    * Die gesamte Struktur der Anweisung variiert.
* **DDL in PL/SQL:** Anweisungen zur Datendefinition (`CREATE TABLE`, `ALTER INDEX`, `DROP USER`, `GRANT` etc.) können **nicht** direkt in PL/SQL geschrieben werden. Sie müssen über Dynamic SQL ausgeführt werden.
* **Ausführung dynamisch erzeugter PL/SQL-Blöcke.**
* **Entwicklung generischer Tools:** Z.B. eine Prozedur, die Daten aus einer beliebigen, als Parameter übergebenen Tabelle löschen kann.

**Native Dynamic SQL (NDS)**

Seit Oracle 8i ist NDS die bevorzugte Methode für Dynamic SQL in PL/SQL. Es ist in die Sprache integriert und verwendet hauptsächlich:

1.  `EXECUTE IMMEDIATE`: Zum Ausführen von Anweisungen, die keine oder genau eine Zeile zurückgeben, oder für DDL und dynamische PL/SQL-Blöcke.
2.  `OPEN FOR`, `Workspace`, `CLOSE` mit Cursor-Variablen: Zum Verarbeiten von dynamischen Abfragen, die mehrere Zeilen zurückgeben (sehen wir uns im nächsten Schritt an).

**Der `EXECUTE IMMEDIATE`-Befehl**

Dies ist das Arbeitspferd für viele dynamische Aufgaben.

* **Syntax (einfachste Form, keine Ein-/Ausgabe):**
    ```sql
    EXECUTE IMMEDIATE 'dynamischer_string';
    -- oder
    EXECUTE IMMEDIATE variable_mit_string_inhalt;
    ```
    * `dynamischer_string`: Eine Zeichenkette (Literal, `VARCHAR2`, `CLOB`), die eine vollständige, gültige SQL-Anweisung (DDL, `INSERT`, `UPDATE`, `DELETE` ohne Rückgabe) oder einen anonymen PL/SQL-Block enthält.

* **Syntax (mit `INTO` für Single-Row-Queries):**
    Ähnlich wie `SELECT INTO`. Der String muss eine `SELECT`-Anweisung sein, die **genau eine Zeile** zurückliefert.
    ```sql
    EXECUTE IMMEDIATE select_string INTO variable1, variable2, ...;
    ```
    * Löst `NO_DATA_FOUND` oder `TOO_MANY_ROWS` aus, wenn nicht genau eine Zeile zurückkommt.

* **Syntax (mit `USING` für Eingabe-Parameter – Bindevariablen):**
    **SEHR WICHTIG!** Damit übergibst du Werte sicher und performant an deine dynamische Anweisung.
    ```sql
    EXECUTE IMMEDIATE sql_string_mit_platzhaltern USING [IN|OUT|IN OUT] bind_wert1, bind_wert2, ...;
    ```
    * `sql_string_mit_platzhaltern`: Enthält Platzhalter (Bindevariablen), meist in der Form `:1`, `:2`, `:n` oder `:beliebiger_name`.
    * `USING`: Liefert die Werte, die zur Laufzeit für die Platzhalter eingesetzt werden sollen. Die Reihenfolge in `USING` entspricht normalerweise `:1`, `:2` usw. Der Modus (`IN`, `OUT`, `IN OUT`) ist nur für dynamische PL/SQL-Blöcke relevant, für SQL-DML sind es effektiv `IN`-Parameter.
    * **Warum Bindevariablen?**
        1.  **Sicherheit:** Verhindert **SQL Injection**! Baue niemals dynamisches SQL durch direkte Verkettung von Benutzereingaben zusammen (`'SELECT * FROM users WHERE name = ''' || user_input || ''''` -> GEFÄHRLICH!). Bindevariablen trennen Code von Daten.
        2.  **Performance:** Oracle kann den Ausführungsplan für den `sql_string_mit_platzhaltern` wiederverwenden, auch wenn sich die Werte in `USING` ändern (Soft Parse statt Hard Parse). Direkte Verkettung erzeugt jedes Mal eine neue Anweisung -> Hard Parse -> langsam.
        3.  **Korrekte Typbehandlung:** Oracle kümmert sich um die Datentypen.

* **Syntax (kombiniert mit `INTO` und `USING`):**
    ```sql
    EXECUTE IMMEDIATE select_string_mit_platzhaltern
       INTO variable1, ...
       USING bind_wert1, ...;
    ```

**Zusammenbau des dynamischen Strings:**
Erfolgt meist mit dem Verkettungsoperator `||`. Achte auf Leerzeichen und vor allem auf Anführungszeichen innerhalb des Strings! Die `q'`-Notation ist hier sehr hilfreich: `q'[SELECT ename FROM emp WHERE job = 'MANAGER']'` ist einfacher als `'SELECT ename FROM emp WHERE job = ''MANAGER'''`.

**Beispiele:**

**1. DDL ausführen:**

```sql
DECLARE
  v_sql VARCHAR2(200);
BEGIN
  session_state_pkg.log_meldung('Erstelle temporäre Tabelle...');
  v_sql := 'CREATE GLOBAL TEMPORARY TABLE my_temp_session_data (
              id NUMBER PRIMARY KEY,
              beschreibung VARCHAR2(100)
            ) ON COMMIT PRESERVE ROWS'; -- Nur für die Session gültig
  EXECUTE IMMEDIATE v_sql;
  session_state_pkg.log_meldung('Temporäre Tabelle erstellt.');

  -- ... hier könnte man die Tabelle nutzen ...

  session_state_pkg.log_meldung('Lösche temporäre Tabelle...');
  EXECUTE IMMEDIATE 'DROP TABLE my_temp_session_data';
  session_state_pkg.log_meldung('Temporäre Tabelle gelöscht.');
EXCEPTION
  WHEN OTHERS THEN
     session_state_pkg.log_meldung('Fehler bei DDL: ' || SQLERRM, 'ERROR');
     -- Optional: Prüfen, ob Tabelle existiert und versuchen zu löschen
     -- RAISE;
END;
/
```

**2. Dynamische Abfrage (Single Row) mit Bindevariable:**
(Wir holen das maximale Gehalt für einen bestimmten Job)

```sql
SET SERVEROUTPUT ON;
DECLARE
  v_job       mitarbeiter.job%TYPE := 'MANAGER';
  v_max_gehalt mitarbeiter.gehalt%TYPE;
  v_sql       VARCHAR2(200);
BEGIN
  v_sql := 'SELECT MAX(gehalt) FROM mitarbeiter WHERE job = :1'; -- Platzhalter :1

  -- Führe String aus, speichere Ergebnis in v_max_gehalt, übergebe v_job für :1
  EXECUTE IMMEDIATE v_sql INTO v_max_gehalt USING v_job;

  DBMS_OUTPUT.PUT_LINE('Maximales Gehalt für Job "' || v_job || '": ' || NVL(v_max_gehalt, 0));

EXCEPTION
  WHEN NO_DATA_FOUND THEN -- Kann bei Aggregatfunktionen ohne GROUP BY nicht auftreten
     DBMS_OUTPUT.PUT_LINE('Keine Daten gefunden (sollte nicht passieren).');
  WHEN OTHERS THEN
     DBMS_OUTPUT.PUT_LINE('Fehler: ' || SQLERRM);
END;
/
```

**3. Dynamisches Update mit Bindevariablen:**

```sql
CREATE OR REPLACE PROCEDURE setze_gehalt (
   p_mnr         IN mitarbeiter.mnr%TYPE,
   p_neues_gehalt IN mitarbeiter.gehalt%TYPE
) IS
   v_sql VARCHAR2(100);
BEGIN
   -- SQL-String mit Platzhaltern für die Werte
   v_sql := 'UPDATE mitarbeiter SET gehalt = :1 WHERE mnr = :2';

   -- Ausführen mit den Werten aus den Prozedur-Parametern
   EXECUTE IMMEDIATE v_sql USING p_neues_gehalt, p_mnr;

   -- SQL%ROWCOUNT gibt die Anzahl der betroffenen Zeilen des letzten DML zurück
   session_state_pkg.log_meldung(SQL%ROWCOUNT || ' Mitarbeiterdatensatz aktualisiert.', 'INFO');

   IF SQL%ROWCOUNT = 0 THEN
      session_state_pkg.log_meldung('Warnung: Kein Mitarbeiter mit MNR ' || p_mnr || ' gefunden für Update.', 'WARN');
   END IF;
   -- Hier wäre ein COMMIT nötig, wenn die Prozedur atomar sein soll.
   -- COMMIT;
EXCEPTION
   WHEN OTHERS THEN
      session_state_pkg.log_meldung('Fehler in setze_gehalt für MNR '|| p_mnr || ': ' || SQLERRM, 'ERROR');
      -- Hier wäre ein ROLLBACK sinnvoll, wenn die Prozedur atomar sein soll.
      -- ROLLBACK;
      RAISE; -- Fehler weiterleiten
END setze_gehalt;
/

-- Aufruf:
BEGIN
  setze_gehalt(p_mnr => 7788, p_neues_gehalt => 3100); -- SCOTT
  setze_gehalt(p_mnr => 9999, p_neues_gehalt => 3000); -- Nicht existierend
END;
/
ROLLBACK; -- Änderungen verwerfen, da kein COMMIT in der Prozedur
```

**4. Dynamischer PL/SQL-Block mit Ein- und Ausgabe:**

```sql
SET SERVEROUTPUT ON;
DECLARE
  v_dynamischer_block VARCHAR2(500);
  v_eingabe_text      VARCHAR2(50) := 'Hallo Welt';
  v_ausgabe_text      VARCHAR2(100);
BEGIN
  -- Anonymer PL/SQL Block als String.
  -- :p1 ist Eingabe, :p2 ist Ausgabe. q'[]' für einfaches String-Handling.
  v_dynamischer_block := q'[
      DECLARE
         l_temp_var VARCHAR2(100) := UPPER(:p1); -- Verwende Eingabe-Bindevariable
      BEGIN
         l_temp_var := l_temp_var || ' (dynamisch verarbeitet)';
         :p2 := l_temp_var; -- Weise Ergebnis der Ausgabe-Bindevariable zu
      END;
  ]';

  -- Führe Block aus, übergebe v_eingabe_text für :p1 (IN), erhalte Wert in v_ausgabe_text für :p2 (OUT)
  EXECUTE IMMEDIATE v_dynamischer_block USING IN v_eingabe_text, OUT v_ausgabe_text;

  DBMS_OUTPUT.PUT_LINE('Eingabe: ' || v_eingabe_text);
  DBMS_OUTPUT.PUT_LINE('Ausgabe aus dyn. Block: ' || v_ausgabe_text);
END;
/
```

**Zusammenfassung Schritt 19:**

* Du verstehst den Unterschied zwischen statischem und dynamischem SQL.
* Du kennst die Anwendungsfälle für Dynamic SQL (Flexibilität, DDL).
* Du kennst `EXECUTE IMMEDIATE` als zentralen Befehl für DDL, DML (ohne Multi-Row-Ergebnis) und dynamische PL/SQL-Blöcke.
* Du verstehst die **entscheidende Bedeutung von Bindevariablen (`USING`)** für Sicherheit und Performance.
* Du hast Beispiele für die verschiedenen Anwendungsfälle von `EXECUTE IMMEDIATE` gesehen.

---

# **Schritt 20: Native Dynamic SQL Teil 2 – Cursor-Variablen (`OPEN FOR`, `Workspace`, `CLOSE`)**

**Das Problem:** `EXECUTE IMMEDIATE ... INTO` funktioniert nur, wenn deine dynamische `SELECT`-Anweisung garantiert genau eine Zeile liefert. Was aber, wenn du eine dynamische Liste von Mitarbeitern, Produkten oder irgendetwas anderem abfragen möchtest?

**Die Lösung: Cursor-Variablen (Ref Cursors)**

Hier kommen Cursor-Variablen (oft Ref Cursor genannt) ins Spiel. Anders als explizite Cursor, die fest an eine bestimmte `SELECT`-Anweisung zur Kompilierzeit gebunden sind, sind Cursor-Variablen flexibler:

* Sie sind **Variablen**, die wie ein Zeiger oder eine Referenz auf den Ergebnisbereich (Result Set) einer Abfrage fungieren.
* Sie können zur Laufzeit mit **unterschiedlichen** (dynamisch erzeugten) `SELECT`-Anweisungen verknüpft werden.

**Der Typ `SYS_REFCURSOR`**

Der gebräuchlichste Typ für Cursor-Variablen, insbesondere im Kontext von Dynamic SQL, ist der vordefinierte *schwache* Ref Cursor `SYS_REFCURSOR`. "Schwach" bedeutet, dass zur Kompilierzeit nicht bekannt ist, welche Spaltenstruktur (Record-Typ) der Cursor zurückgeben wird.

```sql
DECLARE
  cv SYS_REFCURSOR; -- Deklaration einer Cursor-Variable
BEGIN
  -- cv ist hier noch nicht mit einer Abfrage verbunden
  NULL;
END;
```
*(Es gibt auch stark typisierte Ref Cursors (`TYPE name IS REF CURSOR RETURN record_type;`), die mehr Typsicherheit zur Kompilierzeit bieten, aber für rein dynamische Abfragen, bei denen die Spalten vielleicht sogar variieren, ist `SYS_REFCURSOR` flexibler).*

**Der Ablauf mit `OPEN FOR`, `Workspace` und `CLOSE`**

Die Verarbeitung einer dynamischen Multi-Row-Abfrage mit einer Cursor-Variable folgt diesen Schritten:

1.  **`OPEN FOR`:** Verknüpft die Cursor-Variable mit dem dynamischen `SELECT`-String und führt die Abfrage aus. Die Ergebnisse stehen nun im Cursor bereit.
    ```sql
    -- Syntax
    OPEN cursor_variable FOR dynamic_select_string
       [ USING bind_wert1, bind_wert2, ... ];
    ```
    * `cursor_variable`: Die Variable vom Typ `SYS_REFCURSOR`.
    * `dynamic_select_string`: Die `SELECT`-Anweisung als String, kann Platzhalter (`:1`, `:2` etc.) für Bindevariablen enthalten.
    * `USING`: (Optional) Liefert die Werte für die Bindevariablen im String zur Laufzeit. **Genau wie bei `EXECUTE IMMEDIATE` sind Bindevariablen hier essentiell für Sicherheit und Performance!**

2.  **`Workspace`:** Holt die **nächste** Zeile aus dem geöffneten Cursor-Ergebnis und speichert die Werte in Variablen oder einen Record.
    ```sql
    -- Syntax (identisch zu expliziten Cursorn!)
    FETCH cursor_variable INTO variable1, variable2, ...;
    -- Oder:
    FETCH cursor_variable INTO record_variable; -- Record muss zur SELECT-Liste passen
    ```

3.  **Prüfen (`%FOUND`, `%NOTFOUND`):** Nach jedem `Workspace` musst du prüfen, ob noch eine Zeile geholt wurde, um die Verarbeitungsschleife zu beenden. Die Attribute funktionieren genauso wie bei expliziten Cursorn:
    * `cursor_variable%FOUND`: `TRUE`, wenn der letzte `Workspace` erfolgreich war.
    * `cursor_variable%NOTFOUND`: `TRUE`, wenn der letzte `Workspace` keine Zeile mehr fand (Ende erreicht). -> Bedingung zum Verlassen der Schleife.
    * `cursor_variable%ROWCOUNT`: Anzahl der bisher über *diese* Cursor-Variable geholten Zeilen.
    * `cursor_variable%ISOPEN`: `TRUE`, wenn die Variable auf ein geöffnetes Result Set zeigt.

4.  **`CLOSE`:** Gibt die Ressourcen frei, die mit dem Cursor-Ergebnis verbunden sind. **Sehr wichtig**, um Speicher und Cursor-Ressourcen freizugeben!
    ```sql
    CLOSE cursor_variable;
    ```

**Typischer Workflow:**

```sql
DECLARE
  cv          SYS_REFCURSOR;
  v_sql       VARCHAR2(1000);
  v_bind_val  VARCHAR2(100) := 'SOME_VALUE';
  -- Variablen zum Speichern der Ergebnisse
  v_col1      tabelle.spalte1%TYPE;
  v_col2      tabelle.spalte2%TYPE;
BEGIN
  -- 1. Dynamischen String bauen (mit Platzhalter)
  v_sql := 'SELECT spalte1, spalte2 FROM tabelle WHERE irgendeine_spalte = :1 ORDER BY spalte1';

  -- 2. Cursor öffnen
  OPEN cv FOR v_sql USING v_bind_val;

  -- 3. Fetch-Schleife
  LOOP
     -- 4. Nächste Zeile holen
     FETCH cv INTO v_col1, v_col2;
     -- 5. Schleife verlassen, wenn keine Daten mehr da
     EXIT WHEN cv%NOTFOUND;
     -- 6. Daten verarbeiten
     DBMS_OUTPUT.PUT_LINE('Gefunden: ' || v_col1 || ', ' || v_col2);
  END LOOP;

  -- 7. Cursor schließen (!!!)
  CLOSE cv;

EXCEPTION
   WHEN OTHERS THEN
      -- Sicherstellen, dass Cursor auch im Fehlerfall geschlossen wird
      IF cv%ISOPEN THEN
         CLOSE cv;
      END IF;
      RAISE; -- Fehler weiterleiten
END;
/
```

**Cursor-Variablen als Parameter (`OUT SYS_REFCURSOR`)**

Ein sehr häufiges Muster ist, eine Prozedur zu schreiben, die eine komplexe (möglicherweise dynamische) Abfrage ausführt und das Ergebnis als `OUT SYS_REFCURSOR`-Parameter an den Aufrufer zurückgibt. Der Aufrufer kann dann die Daten aus dem Cursor holen. Das ist die Standardmethode, um Abfrageergebnisse aus PL/SQL an Client-Anwendungen (Java, Python, .NET etc.) zu übergeben.

**Beispiel: Dynamische Mitarbeitersuche mit Rückgabe als Cursor**

Die Prozedur sucht Mitarbeiter basierend auf einem Filterkriterium (Spalte und Wert) und einer Sortierspalte, die zur Laufzeit festgelegt werden.

```sql
CREATE OR REPLACE PROCEDURE get_mitarbeiter_dynamisch (
   p_filter_spalte  IN VARCHAR2, -- Spalte für WHERE (z.B. 'JOB' oder 'ABTNR')
   p_filter_wert    IN VARCHAR2, -- Wert für WHERE (z.B. 'CLERK' oder '10')
   p_sortier_spalte IN VARCHAR2, -- Spalte für ORDER BY (z.B. 'NACHNAME' oder 'GEHALT')
   p_ergebnis_cursor OUT SYS_REFCURSOR -- Der OUT-Parameter für das Ergebnis
) IS
   v_sql VARCHAR2(1000);
   -- Sicherheitsvariablen für Spaltennamen
   v_sichere_filter_spalte VARCHAR2(30);
   v_sichere_sortier_spalte VARCHAR2(30);
BEGIN
   -- !!! WICHTIG: Validierung/Sanitisierung von dynamisch eingefügten Spalten-/Objektnamen !!!
   -- Niemals direkt Benutzereingaben für Spalten/Tabellen einfügen! Nur für Werte Bindevariablen nutzen!
   -- Hier: Sehr einfache Whitelist-Prüfung. In echten Anwendungen DBMS_ASSERT verwenden!
   IF UPPER(p_filter_spalte) IN ('JOB', 'ABTNR') THEN
      v_sichere_filter_spalte := DBMS_ASSERT.SIMPLE_SQL_NAME(UPPER(p_filter_spalte)); -- Grundlegende Prüfung
   ELSE
      RAISE_APPLICATION_ERROR(-20201, 'Ungültige Filter-Spalte: ' || p_filter_spalte);
   END IF;

   IF UPPER(p_sortier_spalte) IN ('NACHNAME', 'GEHALT', 'EINSTELL_DATUM', 'MNR', 'JOB', 'ABTNR') THEN
      v_sichere_sortier_spalte := DBMS_ASSERT.SIMPLE_SQL_NAME(UPPER(p_sortier_spalte)); -- Grundlegende Prüfung
   ELSE
      RAISE_APPLICATION_ERROR(-20202, 'Ungültige Sortier-Spalte: ' || p_sortier_spalte);
   END IF;

   -- Dynamisches SQL bauen: Spaltennamen sicher einfügen, Wert über Bindevariable
   v_sql := 'SELECT mnr, nachname, job, gehalt, abtnr ' ||
            'FROM mitarbeiter ' ||
            'WHERE ' || v_sichere_filter_spalte || ' = :filter_wert ' || -- Bindevariable für den Wert!
            'ORDER BY ' || v_sichere_sortier_spalte;

   -- Öffne den OUT-Cursor mit dem dynamischen String und dem Bindewert
   OPEN p_ergebnis_cursor FOR v_sql USING p_filter_wert;

   -- Die Prozedur ist hier fertig. Der Aufrufer muss den Cursor lesen und schließen.

EXCEPTION
   WHEN OTHERS THEN
      session_state_pkg.log_meldung('Fehler in get_mitarbeiter_dynamisch: ' || SQLERRM, 'ERROR');
      -- Versuch nicht, den OUT-Cursor hier zu schließen, das sollte der Aufrufer tun.
      RAISE;
END get_mitarbeiter_dynamisch;
/

-- Aufrufender Block, der den Cursor verarbeitet
SET SERVEROUTPUT ON;
DECLARE
  v_cursor      SYS_REFCURSOR; -- Variable zum Empfangen des Cursors
  v_ma_zeile    mitarbeiter%ROWTYPE; -- Record zum Holen der Daten
  v_filter      VARCHAR2(20);
BEGIN
  -- Test 1: Hole alle 'CLERK's, sortiert nach Gehalt
  DBMS_OUTPUT.PUT_LINE('--- CLERKs sortiert nach GEHALT ---');
  v_filter := 'CLERK';
  get_mitarbeiter_dynamisch(
     p_filter_spalte  => 'JOB',
     p_filter_wert    => v_filter,
     p_sortier_spalte => 'GEHALT',
     p_ergebnis_cursor => v_cursor -- Hier wird der geöffnete Cursor übergeben
  );

  -- Schleife zum Holen der Daten aus dem empfangenen Cursor
  LOOP
     FETCH v_cursor INTO v_ma_zeile;
     EXIT WHEN v_cursor%NOTFOUND;
     DBMS_OUTPUT.PUT_LINE('  MNR: ' || v_ma_zeile.mnr || ', Name: ' || v_ma_zeile.nachname || ', Gehalt: ' || v_ma_zeile.gehalt);
  END LOOP;
  -- Wichtig: Der Aufrufer muss den Cursor schließen!
  CLOSE v_cursor;
  DBMS_OUTPUT.PUT_LINE('Cursor geschlossen. Zeilen: ' || v_cursor%ROWCOUNT);


  -- Test 2: Hole Abteilung 20, sortiert nach Name
  DBMS_OUTPUT.PUT_LINE(CHR(10) || '--- Abteilung 20 sortiert nach NACHNAME ---');
  v_filter := '20';
  get_mitarbeiter_dynamisch('ABTNR', v_filter, 'NACHNAME', v_cursor); -- Cursor wiederverwenden

  LOOP
     FETCH v_cursor INTO v_ma_zeile.mnr, v_ma_zeile.nachname, v_ma_zeile.job; -- Nur benötigte Spalten holen
     EXIT WHEN v_cursor%NOTFOUND;
      DBMS_OUTPUT.PUT_LINE('  MNR: ' || v_ma_zeile.mnr || ', Name: ' || v_ma_zeile.nachname || ', Job: ' || v_ma_zeile.job);
  END LOOP;
  CLOSE v_cursor;
  DBMS_OUTPUT.PUT_LINE('Cursor geschlossen. Zeilen: ' || v_cursor%ROWCOUNT);

EXCEPTION
   WHEN OTHERS THEN
      DBMS_OUTPUT.PUT_LINE('Fehler im aufrufenden Block: ' || SQLERRM);
      -- Sicherstellen, dass der Cursor geschlossen wird, falls er geöffnet wurde
      IF v_cursor IS NOT NULL AND v_cursor%ISOPEN THEN
         CLOSE v_cursor;
         DBMS_OUTPUT.PUT_LINE('Cursor im Fehlerhandler geschlossen.');
      END IF;
END;
/
```

**Zusammenfassung Schritt 20:**

* Du weißt, dass Cursor-Variablen (insb. `SYS_REFCURSOR`) verwendet werden, um dynamische Abfragen mit mehreren Ergebniszeilen zu verarbeiten.
* Du kennst den Ablauf: `OPEN cursor FOR dynamic_string USING binds`, `Workspace cursor INTO vars/record`, `EXIT WHEN cursor%NOTFOUND`, `CLOSE cursor`.
* Du verstehst die Bedeutung von Bindevariablen (`USING`) auch bei `OPEN FOR`.
* Du bist dir der **Sicherheitsrisiken** bewusst, wenn Spalten- oder Objektnamen dynamisch eingefügt werden, und kennst die Notwendigkeit der Validierung (Whitelist, `DBMS_ASSERT`).
* Du hast das gängige Muster gesehen, `SYS_REFCURSOR` als `OUT`-Parameter zu verwenden, um Abfrageergebnisse zurückzugeben.

---
