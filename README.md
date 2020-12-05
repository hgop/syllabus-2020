# Solutions


# Grading

- Pair assignment – 75%
- Exam 25%

# Book

[Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation](https://www.amazon.com/Continuous-Delivery-Deployment-Automation-Addison-Wesley/dp/0321601912)

We will read all chapters in this book. See schedule below.


# Schedule

*Draft might change*

*Birt með fyrirvara um breytingar*

*Slides:* https://reykjavik.instructure.com/courses/3823/modules

*Videos:* https://reykjavik.instructure.com/courses/3823/external_tools/227

[*Practical session 13-14*](https://eu01web.zoom.us/j/63953746447?pwd=NDFPK2J0cVNQbElpZGovdUxFVjlrUT09)

[*Practical session 16-17*](https://eu01web.zoom.us/j/63576957196?pwd=anhsWkhFL3dleklYMGpLbWcxZGQrZz09)

## Week 1

### Day 1: Mon. 23th of November

|                        | Mon                                                      | Tue                                                       | Wed                                                                           | Thu                                                   | Fri                                              |
| ---------------------- | -------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------- | ----------------------------------------------------- | ------------------------------------------------ |
| **Lecture**            | - The Problem of Delivering Software <br>- Video: HGOP 1 and HGOP 2(09:53 - 1:07) <br> - 10:00 Introduction and Q&A [Zoom](https://eu01web.zoom.us/j/68303049072) | - Configuration Management <br>- Video: HGOP 2 (1:35 - 2:01), HGOP 3 (00:00 - 00:49) <br> - 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/68392130813) | - Build and Deployment Scripting <br> - Advanced Version Control <br>- Video: HGOP 3 (1:02) <br> - 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/65734655644) | - The Anatomy of the Build Pipeline <br>- Video: HGOP 4() <br> - 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/67890803409)                  | No lecture, Lab day                              |
| **Reading Assignment** <br> Kaflar innan sviga leggjum við minni áherslu á eða tökum bara hluta af efninu. | Chapter 1                                                | Chapter 2,(3)                                  | Chapter 6,(11),14                                                                  | Chapter 5                                      |                                                  |
| **Lab exercise**       | [Bash](/assignments/week-01/day-01/README.md)            | [Docker & AWS](/assignments/week-01/day-02/README.md)                     | [Terraform & Kubernetes](/assignments/week-01/day-03/README.md)                                             | [Circle CI](/assignments/week-01/day-04/README.md)  | [Week 1 Assignment](/assignments/week-01/day-05/README.md)   |

## Week 2

### Day 1: Mon. 30th of November

|                        | Mon                                                                   | Tue                                                                                              | Wed                                                                  | Thu                                                                          | Fri                                               |
| ---------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------- |
| **Lecture**            | - Implementing a Test Strategy <br> - The Commit Stage <br> 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/66396725335)| - Automated Acceptance Testing<br>- [TDD Demo](https://www.youtube.com/watch?v=dgJ43S_c2XM&feature=youtu.be) <br> - Game intro <br> 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/62752474886) | - Testing Nonfunctional Requirements <br> 11:00 Q&A [Zoom](https://eu01web.zoom.us/j/63184785772)                                   | - 09:00 [Zoom](https://eu01web.zoom.us/j/69068061882) lecture of Information Security. <br> - 10:00 Guest lecture on security from Syndis | No lecture, Lab day                               |
| **Reading Assignment** | Chapter 4,7                                                           |                                                                                                  | Chapter 8                                                            | Chapter 9                                                                    |                                                   |
| **Lab exercise**       | [Pipeline](/assignments/week-02/day-06/README.md)                                | [Unit Testing](/assignments/week-02/day-07/README.md)                                                    | [Acceptance Testing](/assignments/week-02/day-08/README.md) | [Capacity Testing](/assignments/week-02/day-09/README.md) | [Week 2 Assignment](/assignments/week-02/day-10/README.md) |

## Week 3

### Day 1: Mon. 7th of December

|                        | Mon                                                            | Tue                                                                  | Wed                                               | Thu                               | Fri                                                |
| ---------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------- | --------------------------------- | -------------------------------------------------- |
| **Lecture**            | - Deploying and Releasing Applications<br>- Managing Data<br>11:00 Q&A [Zoom]() | [Mobile App Deployment](https://youtu.be/LbNDzrHXaI8) <br> Accessibility<br>Usability<br>11:00 Q&A [Zoom]() | - [Managing Continuous Delivery](https://youtu.be/yuzl_xVM-Oo)
)<br> Team Topology<br> 10:00 Guest lecture on compliance [Zoom]() | Time for students to prepare exam | Exam, 09:00 - 10:30                                |
| **Reading Assignment** | Chapter 10, 12                                                     | Chapter 11                                                           |Chapter 15                                             |                                   |                                                    |
| **Lab exercise**       | [Monitoring](/assignments/week-03/day-11/README.md)|   [Data Migrations](/assignments/week-03/day-12/README.md)                           | [E2E Testing](/assignments/week-03/day-13/README.md) |                                   | [Week 3 Assignment](/assignments/week-03/day-14/README.md) |



# Q&A

### Spurt og svarað

#### Um hvað snýst verkefnavinnan?

Í stuttu máli er þetta hagnýt gæðastjórnun á hugbúnaði, fyrst og fremst frá
sjónarhóli forritara. Það verður komið lítillega inn á stjórnunaraðferðir,
samstarf við prófara, og hugsanlega verður eitthvað farið dýpra í
nýtileikaprófanir (usability testing). Verkefnavinna snýst að verulegu leyti um
sjálfvirknivæðingu á uppsetningarferli, einingaprófanir, sjálfvirkar
viðmótsprófanir og aðferðir til að tryggja gæði í afhendingu hugbúnaðar.

#### Hvernig er fyrirkomulag námskeiðsins?

Megináherslan í námskeiðinu er á verkefnavinnu. Í lok hverrar viku eru
verkefnaskil, hvert verkefni um sig gildir 25%. Verkefnin á að vinna í pörum, ef
tveir vinna saman viljum við sjá jafnt framlag frá báðum aðilum inn á Git
history, það er því nauðsynlegt að skipta reglulega um Driver.


Við breyttar aðstæður verður fyrirkomulagið örlítið frábrugðið síðustu árum og gerum við ráð fyrir að kenna það að mestu leiti í fjarkennslu. 
Dagarnir skiptast þannig að fyrir hádegi er bóklegt nám og svo verklegt eftir hádegi. Við gerum ráð fyrir að nota eitthvað af upptökum frá því í fyrra sem við gerum ráð fyrir að þið horfið á um morguninn og svo verðum við með spurningatíma kl 11:00 á Zoom. 
Við stefnum svo á að vera með tvo Zoom tíma í tengslum við verkefnið fyrst kl 13:00 - 14:00 og svo aftur kl 16:00-17:00. Við sjáum hvernig þetta kemur út, gætum aðlagað þetta eitthvað þegar við sjáum hvernig þetta kemur út. 

#### Er hægt að senda fyrirspurnir Piazza ?

Nei, við verum ekki með Piazza heldur ætlum við að notast frekar við Issues hér
á GitHub. Við viljum að þið æfist í því að lýsa vandamálum á skipulagðan hátt.

#### Verða fyrirlestrar teknir upp?

Já þeir verða teknir upp, linkur á þá verður settur inn á Canvas undir Modules.

#### Hvenær lýkur námskeiðinu, þ.e., hvenær er próf?

Að öllu óbreyttu verður prófið haldið síðasta dag námskeiðsins og skil á síðasta
verkefninu síðar þann dag. Til að standast áfangann verða nemendur að fá yfir
4,75 á prófinu.

#### Hversu vont mál að missa úr tíma?

Efni fyrirlestranna er að finna að miklu leyti í bókinni, ættir því að geta
haldið í við efnið með því að lesa viðkomandi kafla extra-vel.

Aftur á móti þá eru líka kynningar á verkefnum dagsins í tímum.

#### Hvernig er fyrirkomulagið á verkefnavinnunni, og hversu miklu álagi má búast við?

Það er erfitt að meta álagið nákvæmlega, það fer verulega eftir hvaða kunnáttu
menn koma með inn í kúrsinn. Við munum gera okkar besta í að halda álaginu
hóflegu, þ.e. c.a. 9-5 vinna ætti að duga flestum.

#### Þarf ég að hafa Linux uppsett á vélinni minni?

Byggt að reynslu síðustu ára mælum við með að allir nemendur séu að vinna á
Linux, og þá helst nýlega útgafú af Ubuntu. Það er hægt að keyra það í sýndarumhverfi
eins og t.d. VirtualBox, setja upp dual-boot, eða keyra upp á minnislykli.
Einnig er mögulegt að vinna verkefnið á Mac OsX, og munu dæmatímakennarar styðja
við það. Nemendur þurfa ásamt því að vera með góðan editor á vélinni sinni, svo
sem VS Code, WebStorm, SubLime, Atom eða sambærilegt. **Æskilegt er að hafa vél
með 8GB í minni, 4GB ætti að sleppa fyrir horn.** Við munum gefa út
leiðbeiningar um uppsetningu síðar.
