# ilo shelter — lipu pi nasin sama: toki tan ona sama (toki pona)

> **toki lili pi lipu ni:** lipu ni li lipu pi kulupu, li tan nasin NTARI "P2-002". lipu suli li lipu Inli "CONFORMANCE.md" (tenpo pi 2026-10-05). ilo sona li pali e lipu ni; kulupu li wile lukin e ona kepeken nasin "P2-002" kipisi "3.1".
>
> **Note:** This is a community rendering under NTARI policy P2-002. The complete document is the English original CONFORMANCE.md (snapshot 2026-10-05). Machine-assisted draft pending community review per P2-002 section 3.1.
>
> sina lukin e pakala lon toki ni la o pona e ona: o pali e "fork" lon
> https://github.com/NTARI-RAND/shelter, o pana e "pull request". pana sina
> li pona tawa mi mute.
>
> **lipu Inli li lawa.** lipu ni li tawa lukin, li tawa ala sona pi nasin lawa: lipu ni li ante tan `CONFORMANCE.md` la lipu Inli li lawa. nimi lawa (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY), nimi pi poki sona, nimi sitelen en nimi ilo li awen lon toki Inli, tan ni: ona li lipu lawa pi nasin sama.

---

*ilo pi lukin pona (test suite) li toki ala e nimi nanpa ni la, ilo shelter li toki e ni tan ona sama taso (lipu nimi: `JFA/jfa-conformance-suite.py`). ilo shelter li lon tenpo pi wile pali taso (proposal-stage); "met by design" (pona tan nasin) li toki e ni: lipu nasin li toki e lawa, li wile e lukin pona (test).*

**selo:** esun pi pana pona tawa jan pi kama sin lon kulupu (reintegration support) lon kipisi VI pi nasin JFA — lukin tan poka tu (bidirectional assessment); ilo li pali nanpa wan kepeken linja ala (offline-first) lon ilo pi mani lili (entry-level devices); pona jan (dignity) en ken weka (exit) li lon sewi pi lukin lawa (oversight). ona li jo e nimi: ona li ijo pi ike nanpa wan tawa pakala nanpa 8 pi pini ala.

| nimi nanpa (ID) | nasin awen | lon seme |
|---|---|---|
| COV-1 | nanpa meso ala; mute ale pi pana | **pona tan nasin (Met by design)** — INV-4; lipu li wile e linja awen kepeken grep lon ilo CI |
| COV-2 | pana pi anpa nanpa wan li pakala pi toki awen | **pona tan nasin (Met by design)** — −1 li jo e sitelen ike (harm-flagged), toki li wile, jan pi pana pona (caseworker) li kama sona |
| COV-3 | sama tawa poka tu; jan li ken toki sin tawa toki ale | **pona tan nasin (Met by design)** — pana nanpa li tan poka tu; nasin utala (dispute path) li pana e toki poka, li weka ala e toki lon tenpo ale |
| COV-4 | ona li toki e ni: "ken anu ken ala?", li toki ala e mute lon tenpo ale | **pona tan nasin (Met by design)** — nanpa ala li lawa e suli pi jan ale; mute (count) li sitelen pona, li nanpa ala pi sewi en anpa (rank) |
| REC-1 | lipu li kama suli taso (append-only) | **pona tan nasin (Met by design)** — pana nanpa li ken ala ante (immutable rating events); linja pi ken weka en awen lon tenpo ale li pini ala (OQ-1) |
| REC-2 | o weka e pilin ike, o len ala lon tenpo ale | **pona tan nasin (Met by design)** — sitelen ike en utala li pana e sona poka |
| REC-3 | sona pi jan ("PII") li lon ala ijo ale pi kama pana | **pona tan nasin (Met by design)** — INV-5/6/8/10: nimi len; sona pi jan ("PII") li lon ala lipu pi pali ilo (logs); o lukin e lon, o awen ala (verify-don't-retain); kulupu pi lon jan (status categories) li lon ala |
| REC-9 | kule pi linja jan (relation types) | **kipisi taso (Partial)** — pana nanpa pi kule poka (direction-typed ratings) (citizen↔beneficiary, inspection) li kama sama pona; kule lawa (formal typing) li kama lon tenpo pali |
| GOV-9 | lipu pi wile sona pi pini ala li lon, li ante lon tenpo ale | **pona (Met)** — `OPEN-QUESTIONS.md` li kama poka lipu open ni |
| GOV-10 | poki pi jan ale (copyleft) | **pini ala (Open)** — kepeken sin pi ilo LBTAS anu pali sin e ona (OQ-3) |
| Problem 8 | mani anpa pi jan lukin (witness cost floor) | **jo e nimi (Named)** — lipu awen li tan ona sama taso, tawa tenpo ni: jan li ken pana e mani tawa jan lukin lon ma ni (OQ-2) |

**toki lon:** lipu awen pi jan lukin li lon ala; lukin ale pi nimi pona (reputation read) li tan toki pi jan lawa ilo (operator-attested) taso. toki "we don't collect that" (mi kama jo ala e ni) pi INV-10 li nasin awen (defense) lon nasin pi ilo ni (architectural); nasin awen ni o awen lon ante ale kama pi selo sona (schema).
