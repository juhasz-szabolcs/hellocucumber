# BDD és Cucumber - Egyszerű magyarázat

## Mi az a BDD?

**BDD** = **Behavior-Driven Development** (Viselkedésvezérelt fejlesztés)

A BDD egy olyan módszer, ahol a szoftver fejlesztését a **viselkedés** és a **funkciók** vezérlik, nem pedig a technikai részletek.

### Miért jó a BDD?

1. **Mindenki érti**: A teszteket közérthető nyelven írjuk, nem programozói nyelven
2. **Közös nyelv**: A fejlesztők, tesztelők és üzleti szakértők is megértik
3. **Dokumentáció**: A tesztek egyben dokumentációként is szolgálnak
4. **Korai hibakeresés**: Már a fejlesztés elején láthatjuk, mit kell csinálni

### Hogyan működik?

A BDD-ben a követelményeket **scenáriókként** írjuk le, amelyek így néznek ki:

```
Given (Adott) - Kiindulási állapot
When (Amikor) - Művelet
Then (Akkor) - Várt eredmény
```

Ez olyan, mintha egy történetet mesélnénk el a számítógépnek!

---

## Mi az a Cucumber?

A **Cucumber** egy eszköz, amely lehetővé teszi, hogy **közérthető nyelven** írjunk teszteket, és ezeket automatikusan futtassuk.

### Főbb előnyök:

- **Gherkin nyelv**: Egyszerű, angol-szerű nyelv a tesztek írásához
- **Több nyelv támogatása**: Java, JavaScript, Python, stb.
- **Olvasható tesztek**: A tesztek olyanok, mint egy történet
- **Automatizálás**: A teszteket automatikusan futtathatjuk

---

## A projekt struktúrája

Nézzük meg, hogyan van felépítve ez a projekt:

```
hellocucumber/
├── pom.xml                          # Maven konfiguráció (függőségek)
├── src/
│   ├── main/
│   │   └── java/
│   │       └── hellocucumber/
│   │           └── IsItFriday.java         # Alkalmazás kód (amit tesztelünk)
│   └── test/
│       ├── java/
│       │   └── hellocucumber/
│       │       ├── RunCucumberTest.java    # Teszt futtató osztály
│       │       └── StepDefinitions.java    # Teszt kód (ami teszteli)
│       └── resources/
│           └── hellocucumber/
│               └── is_it_friday_yet.feature  # Feature fájl (tesztek)
```

### Fontos fájlok:

1. **`IsItFriday.java`** (src/main/java): Az alkalmazás kódja, amit tesztelünk
2. **`.feature` fájl** (src/test/resources): Itt írjuk le a teszteket közérthető nyelven
3. **`StepDefinitions.java`** (src/test/java): Itt írjuk meg a teszt kódot, ami teszteli az alkalmazás kódot
4. **`RunCucumberTest.java`** (src/test/java): Ez futtatja a teszteket
5. **`pom.xml`**: Itt vannak a szükséges könyvtárak (Cucumber, JUnit, stb.)

---

## Részletes magyarázat

### 1. A Feature fájl (`is_it_friday_yet.feature`)

```gherkin
Feature: Is it Friday yet?
  Everybody wants to know when it's Friday

  Scenario Outline: Today is or is not Friday
    Given today is "<day>"
    When I ask whether it's Friday yet
    Then I should be told "<answer>"

  Examples:
    | day            | answer |
    | Friday         | TGIF   |
    | Sunday         | Nope   |
    | anything else! | Nope   |
```

#### Mit jelent ez?

- **Feature**: Ez egy funkció leírása ("Vajon már péntek van?")
- **Scenario Outline**: Ez egy teszt sablon, amelyet több adattal is futtathatunk
- **Given**: Kiindulási állapot - "Adott, hogy ma X nap van"
- **When**: Művelet - "Amikor megkérdezem, hogy már péntek van-e"
- **Then**: Várt eredmény - "Akkor X választ kell kapnom"
- **Examples**: Táblázat, amely tartalmazza a különböző teszt adatokat

#### Hogyan működik?

1. Első sor: `Friday` → Várjuk: `TGIF`
2. Második sor: `Sunday` → Várjuk: `Nope`
3. Harmadik sor: `anything else!` → Várjuk: `Nope`

Ez három különböző tesztet jelent!

---

### 2. A Step Definitions (`StepDefinitions.java`)

Ez a fájl tartalmazza a Java kódot, amely **valójában végrehajtja** a teszteket.

```java
@Given("today is {string}")
public void today_is(String today) {
    this.today = today;
}
```

#### Mit csinál ez?

- A `@Given` annotáció azt mondja: "Ha a feature fájlban ezt a sort látod, akkor ezt a metódust hívd meg"
- A `{string}` egy paraméter, amelyet a feature fájlból kap
- A metódus eltárolja, hogy ma melyik nap van

```java
@When("I ask whether it's Friday yet")
public void i_ask_whether_it_s_Friday_yet() {
    actualAnswer = IsItFriday.isItFriday(today);
}
```

- Ez a metódus meghívja az `IsItFriday.isItFriday()` függvényt
- Ez ellenőrzi, hogy péntek van-e, és visszaadja a választ

```java
@Then("I should be told {string}")
public void i_should_be_told(String expectedAnswer) {
    assertThat(actualAnswer).isEqualTo(expectedAnswer);
}
```

- Ez ellenőrzi, hogy a kapott válasz megegyezik-e a várt válasszal
- Ha nem, akkor a teszt **sikertelen**

#### Az IsItFriday osztály - Ez az alkalmazás kódja!

Az `IsItFriday` osztály a `src/main/java/hellocucumber/IsItFriday.java` fájlban található:

```java
class IsItFriday {
    static String isItFriday(String today) {
        return "Friday".equals(today) ? "TGIF" : "Nope";
    }
}
```

**Fontos megjegyzés**: Ez az osztály **nem a teszt része**, hanem az **alkalmazás kódja** (production code), amit tesztelünk!

Ez egy egyszerű logika:
- Ha ma péntek → visszaadja: `"TGIF"`
- Ha nem péntek → visszaadja: `"Nope"`

**Mi a különbség?**

- **`IsItFriday`** = Alkalmazás kód (amit tesztelünk) - ez az, amit a fejlesztők írnak
  - Helye: `src/main/java/hellocucumber/IsItFriday.java`
- **`StepDefinitions`** = Teszt kód (ami teszteli) - ez az, amit a tesztelők írnak
  - Helye: `src/test/java/hellocucumber/StepDefinitions.java`

**Projekt struktúra:**

A projekt most már helyesen van felépítve:

```
src/
├── main/java/              # Alkalmazás kód (amit tesztelünk)
│   └── hellocucumber/
│       └── IsItFriday.java
└── test/java/              # Teszt kód (ami teszteli)
    └── hellocucumber/
        ├── StepDefinitions.java
        └── RunCucumberTest.java
```

A teszt kód (`StepDefinitions`) **meghívja** az alkalmazás kódot (`IsItFriday`), és **ellenőrzi**, hogy helyesen működik-e!

---

### 3. A RunCucumberTest (`RunCucumberTest.java`)

Ez az osztály felelős a tesztek futtatásáért.

```java
@Suite
@IncludeEngines("cucumber")
@SelectPackages("hellocucumber")
```

- `@Suite`: Ez egy teszt csomag
- `@IncludeEngines("cucumber")`: Cucumber motor használata
- `@SelectPackages("hellocucumber")`: A `hellocucumber` csomagban lévő tesztek futtatása

---

## Hogyan működik együtt?

### A folyamat lépései:

1. **Futtatjuk a tesztet** (pl. Maven-nel vagy IDE-ből)
2. **Cucumber elolvassa** a `.feature` fájlt
3. **Minden sorhoz** megkeresi a megfelelő Java metódust a `StepDefinitions.java`-ban (teszt kód)
4. **A teszt kód meghívja** az alkalmazás kódot (`IsItFriday`)
5. **Az alkalmazás kód végrehajtódik** és visszaadja az eredményt
6. **A teszt kód ellenőrzi** az eredményeket
7. **Jelentést készít** arról, hogy sikeresek voltak-e a tesztek

**Fontos**: A teszt kód **meghívja** és **teszteli** az alkalmazás kódot!

### Példa folyamat:

```
Feature fájl: "Given today is 'Friday'"
         ↓
[Teszt kód] StepDefinitions: @Given("today is {string}") → today_is("Friday")
         ↓
Feature fájl: "When I ask whether it's Friday yet"
         ↓
[Teszt kód] StepDefinitions: @When(...) → i_ask_whether_it_s_Friday_yet()
         ↓
[Alkalmazás kód] IsItFriday.isItFriday("Friday") → "TGIF"  ← Ezt teszteljük!
         ↓
Feature fájl: "Then I should be told 'TGIF'"
         ↓
[Teszt kód] StepDefinitions: @Then(...) → i_should_be_told("TGIF")
         ↓
[Teszt kód] assertThat("TGIF").isEqualTo("TGIF") → ✅ SIKERES!
```

**Látható**: A teszt kód meghívja az alkalmazás kódot (`IsItFriday`), és ellenőrzi, hogy helyesen működik-e!

---

## Hogyan futtathatjuk a teszteket?

### Maven-nel (parancssorból):

```bash
mvn test
```

### IDE-ből:

- Kattints jobb gombbal a `RunCucumberTest.java` fájlra
- Válaszd a "Run" opciót

### Mi történik futtatáskor?

1. A Cucumber beolvassa a feature fájlt
2. Minden Examples sorhoz külön tesztet futtat
3. Kiírja az eredményeket (sikeres/sikertelen)
4. Jelentést készít

---

## Alkalmazás kód vs. Teszt kód

### Mi a különbség?

Ez egy **nagyon fontos** fogalom, amit mindenképpen meg kell érteni!

#### Alkalmazás kód (Production Code)

- Ez az, amit **valójában használunk** az alkalmazásban
- A `src/main/java` mappában van (valós projektekben)
- Ezt a kódot a **fejlesztők** írják
- **Példa**: Az `IsItFriday` osztály - ez az alkalmazás egy része

```java
// Ez az alkalmazás kódja - ezt használjuk az alkalmazásban
class IsItFriday {
    static String isItFriday(String today) {
        return "Friday".equals(today) ? "TGIF" : "Nope";
    }
}
```

#### Teszt kód (Test Code)

- Ez az, ami **teszteli** az alkalmazás kódot
- A `src/test/java` mappában van
- Ezt a kódot a **tesztelők** vagy **fejlesztők** írják
- **Példa**: A `StepDefinitions` osztály - ez teszteli az `IsItFriday` osztályt

```java
// Ez a teszt kódja - ez teszteli az alkalmazás kódot
@When("I ask whether it's Friday yet")
public void i_ask_whether_it_s_Friday_yet() {
    actualAnswer = IsItFriday.isItFriday(today);  // Meghívja az alkalmazás kódot
}
```

### Hogyan működik együtt?

1. Az **alkalmazás kód** (`IsItFriday`) tartalmazza az üzleti logikát
2. A **teszt kód** (`StepDefinitions`) meghívja az alkalmazás kódot
3. A **teszt kód** ellenőrzi, hogy az alkalmazás kód helyesen működik-e

### Miért fontos ezt különválasztani?

- **Tisztaság**: Világos, hogy mi az alkalmazás és mi a teszt
- **Karbantarthatóság**: Könnyebb módosítani és bővíteni
- **Újrahasználhatóság**: Az alkalmazás kódot más helyeken is használhatjuk
- **Best practice**: Ez az ipari szabvány

### Ebben a példában

Ebben a példában az `IsItFriday` osztály már a megfelelő helyen van: a `src/main/java/hellocucumber/IsItFriday.java` fájlban. Ez a **helyes struktúra**, amelyet valós projektekben is használunk!

---

## Főbb fogalmak

### Given-When-Then (GWT)

Ez a BDD alapvető struktúrája:

- **Given (Adott)**: Kiindulási állapot beállítása
- **When (Amikor)**: Művelet végrehajtása
- **And (És)**: További feltételek vagy műveletek
- **Then (Akkor)**: Eredmény ellenőrzése

### Scenario vs Scenario Outline

- **Scenario**: Egy konkrét teszt eset
- **Scenario Outline**: Teszt sablon, amelyet több adattal futtathatunk (Examples táblázattal)

### Step Definitions

A Java metódusok, amelyek a feature fájlban lévő sorokat valósítják meg.

### Glue Code

A Step Definitions kódja, amely "összeragasztja" a feature fájlt a Java kóddal.

---

## Miért hasznos ez?

### Előnyök:

1. **Olvashatóság**: Bárki megérti a teszteket, nem csak programozók
2. **Dokumentáció**: A tesztek egyben dokumentációként is szolgálnak
3. **Karbantarthatóság**: Könnyű módosítani és bővíteni
4. **Közös nyelv**: Fejlesztők, tesztelők, üzleti szakértők is értik
5. **Korai visszajelzés**: Gyorsan láthatjuk, ha valami nem működik

### Példa valós életből:

Helyett, hogy így írnánk:
```java
@Test
public void testFriday() {
    assertEquals("TGIF", isItFriday("Friday"));
}
```

Így írjuk:
```gherkin
Given today is "Friday"
When I ask whether it's Friday yet
Then I should be told "TGIF"
```

A második verzió **sokkal olvashatóbb** és **értelmesebb**!

---

## Összefoglalás

- **BDD** = Viselkedésvezérelt fejlesztés, ahol közérthető nyelven írjuk a teszteket
- **Cucumber** = Eszköz, amely lehetővé teszi a Gherkin nyelven írt tesztek futtatását
- **Feature fájl** = Itt írjuk le a teszteket közérthető nyelven
- **Step Definitions** = Itt írjuk meg a teszt kódot (Java), amely teszteli az alkalmazás kódot
- **Alkalmazás kód** = Az a kód, amit tesztelünk (pl. `IsItFriday` osztály)
- **Teszt kód** = Az a kód, ami teszteli az alkalmazás kódot (pl. `StepDefinitions`)
- **Given-When-Then** = A BDD alapvető struktúrája

**Fontos megjegyzés**: A teszt kód **meghívja** és **ellenőrzi** az alkalmazás kódot. Az alkalmazás kód az, amit valójában használunk, a teszt kód pedig azt ellenőrzi, hogy helyesen működik-e!

Ez a módszer segít abban, hogy a tesztek **olvashatóak**, **értelmesek** és **könnyen karbantarthatók** legyenek!

---

## További tanulás

Ha többet szeretnél megtudni:

- [Cucumber hivatalos dokumentáció](https://cucumber.io/docs/cucumber/)
- [Gherkin nyelv referencia](https://cucumber.io/docs/gherkin/reference/)
- [BDD bevezető](https://cucumber.io/docs/bdd/)

---

*Ez a dokumentáció a `hellocucumber` projekthez készült, hogy segítsen megérteni a BDD és Cucumber alapjait.*
