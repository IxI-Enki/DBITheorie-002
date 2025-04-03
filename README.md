# DBITheorie-002

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

# Tiefer in die Themen
> Ein mächtiges, aber auch mit Vorsicht zu genießendes Werkzeug in Oracle: **Trigger**.

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

.. 🥱 .. TO BE CONTINUED .. 🥱 ..
