---
title: 2 – הכנת הנתונים
author: nirgn
layout: post
summary: ""
category: Information Theory
---
בפוסט הזה נדבר על בעיות באיכות נתונים, נסקור כמה שיטות לתצוגה גרפית של נתונים, נדבר על שיטות כמותיות לניקוי נתונים, נעבור לשילוב והמרה של נתונים המגיעים ממקורות שונים, נתייחס בקצרה לבעיות של הכנת נתונים תלויי זמן, ולבסוף נעבור על שיטות סיווג וחיזוי, נעריך את הדיוק והשגיאה שלהן ונראה כיצד להשוות בין שיטות שונות שלהן.

**בפוסט הזה נדבר על הנושאים:**

1. בעיות באיכות נתונים
2. ייצוג גרפי של נתונים
3. ניקוי נתונים
4. שילוב והמרות של נתונים
5. סיווג vs וחיזוי
6. מדדים להערכת דיוק ושגיאה
7. שיטות חיזוי (מודלים של רגרסיה)
8. השוואה בין מסווגים

<!--more-->

&nbsp;

### 1. בעיות באיכות נתונים
כשהנתונים מגיעים מהעולם האמיתי הם לא תמיד “מושלמים”, ואנחנו צריכים להתאים אותם למה שהכלים של כריית מידע זקוקים / מבקשים. ז”א לעמוד בתנאים שהכלים מציבים לנו, על מנת שאלה ידעו מה לעשות עם המידע, ויצלחו להפיק ממנו ידע. אז מה הכלים של כריית מידע צרכים?

דבר ראשון, שהמידע יהיה זמין, כמובן שאם אנו לא יכולים לספק להם את המידע (בצורה שהם מכירים, כמו לדוגמה כ csv או כ arff).
בנוסף, אנו זקוקים לכל המידע בטבלה אחת, ז”א שאם המידע מגיע מכמה מקורות, יש לאחד בין טבלאות המידע.
אנחנו (איש כריית המידע) צריך להבין את המשמעות של כל משתנה (כדי שנוכל לקבוע מראש האם גורם מסויים הוא משתנה מסביר אפשרי או שמדובר במשתנה תלוי – כזה שאנו מעוניינים לנבא).
תחום ההגדרה של כל משתנה צריך להיות מוגדר וידוע מראש (אם מדובר במשתנה קטגורי, אז מדובר על סט ערכים ידוע מראש, לדוגמה ימים בשבוע. או אם מדובר על משתנה רציף, אז מדובר על קטע בין ערך מינימום למקסימום, לדוגמה ציונים ניתנים בתחום של 0-100).
על כל הערכים של המשתנים (בכל אחד מהתצפיות) להיות ידועים מראש, על הנתונים להיות אמינים (המידע יהיה נכון / אמיתי. אנחנו לא מצפים למצוא שגיאות בתוך הנתונים שלנו כשאנו מריצים את האלגו’ לכריית מידע), ולבסוף, אין להכיל מידע כפול.


**מדוע הנתונים ב”עולם האמיתי” “מלוכלכים”?**

במקרים מסויימים נגלה שלפעמים חסרים ערכים מסויימים ברשומות כלשהן. מצב זה קורה כשלא ניתן להשיג את הערך של אותו משתנה בזמן שהנתונים נאספו, כמו לדוגמה שאין לנו את הציון של הסטודנט כל עוד אינו סיים ללמוד את הקורס, וכד’. הבעיות האלו יכולות לנבוע גם מבעיות טכניות (בעיות אנוש, חומרה, תוכנה). בעיות נוספות בנתונים יכולים לנבוע מרעש הנוצר כתוצאה מבעיות טכניות הקיימות בציוד (במידה והנתונים נאספים באופן אוטומטי), ואז בעיה בציוד יכולה לגרום לנו לאסוף נתונים שגויים. כמובן שגם כאן יכולות להתבצע טעויות אנוש (אם זה נתונים המוקלדים למחשב, לדוגמה). לבסוף, הסיבה האחרונה ל”לכלוך” הנתונים היא קיום של מספר מקורות מידע, שיכולים להביל למידע לא אחיד.

לאור כל זה, לאחר שראינו שהנתונים יכולים להיות חסרים ו/או מלוכלכים (שגויים, לא מדוייקים, וכ’ו). אנו מבינים כי ישנה חשיבות להכנה מוקדמת של הנתונים. הרי שאם אין לנו נתונים איכותיים, קשה לנו לצפות לתוצאות איכותיות מהתליך כריית המידע. תהליך הכנת המידע כולל: ניקוי הנתונים, שילוב נתונים מטבלאות שונות, המרות של נתונים, וצמצום טבלת הנתונים (במידת האפשר).

 &nbsp;

### 2. ייצוג גרפי של נתונים
לאחר איסוף הנתונים, נציג אותם בצורה גרפית באמצעות היסטוגרמה, תרשים פיזור, ועוד, במטרה לזהות נתונים שיש לנקותם כדוגמת נתונים שמחוץ לתחום או לא עקביים.

**היסטוגרמה**
מטרת ההיסטוגרמה ([Histogram](http://en.wikipedia.org/wiki/Histogram)) היא לעזור לנו לזהות את הערכים בעלי השכיחות הגבוהה ביותר, ערכים נדירים, וערכים הנופלים בתחום שאינו סביר. תרשים ההיסטוגרמיה מתייחס תמיד למשתנה אחד, ובדרך כלל, נבנה אותה עבור משתנה רציף, כאשר התחום מחולק למספר קטעים (בעלי רוחב שווה). בצד שמאל, ניתן לראות דוגמה לניתוח היסטוגרמה.

נבנה את ההיסטוגרמה עפ”י השלבים הבאים: 1. נסדר את הערכים בסדר עולה. 2. נחלק את תחום הנתונים למרווחים (יש להקפיד שהמרווחים לא יהיו רחבים מידי או צרים מידי, כדי שמצד אחד לא נאבד מידע אך מצד שני שההיסטוגרמה לא תיהיה חלקה. כלל אצבע הוא לקבוע את מספר קבוצות המרווחים כבערך שורש גודל הדגימה). 3. נקבע עבור כל מרווח את התדירות ונבנה את טבלת התדירות הכוללת את המרווח. 4. נשרטט את התפלגות התדירות (ציר x יציין את המרווחים וציר y את התדירויות).

**תרשים פיזור**
תרשים פיזור ([Scatter plot](http://en.wikipedia.org/wiki/Scatter_plot)) או תרשים XY מציג את קשרי הגומלין בין הערכים המספריים במספר סדרות נתונים, או שהוא מתווה שתי קבוצות מספרים כסדרה אחת של קואורדינטות XY. בעזרת תרשים פיזור אנו יכולים להסיק מהר מאוד מסקנה כללית לגבי אופי המתאם בין 2 משתנים, אך אנו גם יכולים למצוא ביטוי ע”י נוסחה סטטיסטית בעזרת החלקה של הפיזור.

לעקומה (או במקרה שלנו, בתמונה משמאל, לקו הישר) קוראים Loess (ראשי תיבות של Local Regression) או בעברית “רגרסיה מקומית”. במקרה של רגרסיה לינארית, ניתן לבטא באופן כמותי את הקשר (המתאם) בין שני המשתנים X,Y בעזרת השונות המשותפת (Covariance) שתסומן כ (COV(X,Y. השונות המשותפת היא (COV(X,Y) = E(X,Y) – E(X) * E(Y כאשר E מציין תוחלת (הערכים המתקבלים יכולים להיות בין מינוס אינסוף לאינסוף). כאשר COV(X,Y)=0 המשמעות היא שהמשתנים האקראיים X ו Y בלתי מותאמים.

מכיוון שהשונות המשותפת תלויה ביחידות המדידה, נגדיר את מקדם המתאם (קורלציה) שיסומן ב p ומהווה סטנדרטיזציה של השונות המשותפת. מקדם המתאם הוא [p(X,Y) = [COV(X,Y)]/[σx*σy כאשר σx ו σy הן סטיות התקן של x ו y (ערכו של מקדם המתאם הוא בין 1- ל 1+).

  &nbsp;

### 3. ניקוי נתונים
בחלק זה אנו מתמקדים בשאלה “כיצד ניתן לשפר את איכות הנתונים?” ונבצע מספר פעולות עקריות על מנת להשיג את מטרה זו:

* **נשלים ערכים חסרים** – כיצד ניתן להתמודד עם ברשומה מסוימת ישנם מספר משתנים עם ערך לא ידוע? הדבר הקל ביותר הוא להתעלם מאותה רשומה, אך זה אינו מומלץ כי אנו נאבד את כל המידע הקשור אליה. אך לפעמים לא תיהיה לנו ברירה וניהיה חייבים לבצע זאת (לדוגמה, במקרה בו אנו מסווגים את הנתונים, וחסר לנו הנתון לפיו אנו מסווגים את הרשומות). מנגד, אפשר לנסות ולהשלים את הנתון באופן ידני, אך אם מדובר על בסיס נתונים גדול אופציה זו אינה פרקטית כאשר אחוז הנתונים החסרים גדול מידי (או שהנתונים הוזנו לפני זמן רב עד כדי שלא מתאפשר לנו להשלים אותם). לבסוף, אם אין בידנו ברירה אחרת, נשלים את הנתונים באופן אוטומטי – נשתמש בקבוע המייצג את הנתון החסר, או נשלים את הערך החסר באמצעות הערך הממוצע של המשתנים, או אפילו להשלים את הערך החסר באמצעות הערך הממוצע של המשתנים מאותו הסיווג.
* **נזהה ערכים חריגים ונחליק נתונים רועשים** – השיטה המקובלת ביותר להתמודדות עם צמצום השונות בתוך הנתונים היא Binning או חלוקה לקטעים – תיבות. נחלק את התצפיות לתיבות שיהיו בעלי שכיחות שווה, ואז ניתן להחליק את כל התצפיות באותה תיבה לפי הממוצע של התיבה / על פי החציון של התיבה / או הגבולות של אותה התיבה (כל ערך מוחלף בגבול העליון או התחתון של התיבה). ישנן שיטות נוספות להחלקה של ערכים, כמו באמעות מודל של רגריסה בו אנו נבצע שימוש אם יש קשר לינארי בין 2 משתנים, או ב Clustering – חלוקה אשכולות, אם מדובר בנתונים רב מימדיים (נזהה ונסלק תצפיות חריגות).
* **נסלק רשומות כפולות** – נמחק רשומות כפולות, כאלו שנוצרו כתוצאה משילוב של נתונים ממקורות שונים.


> אוסף הנתונים עשויים להיות רשומה, וקטור, תבנית, מאורע, דגימה, תצפית, ישות ועוד. הנתונים מותארים על ידי מספר תכונות כדוגמת מאפיין, משתנה, שדה, מימד ועוד.

> מקובל להבחין בין תכונות איכותיות המכונות תכונות קטגוריאליות (ארצות, מגדר, שביעות רצון {גבוהה, בינונית, נמוכה}, לבין תכונות כמותיות המכונות תכונות נומריות (ציונים בקורס, גובה, משקל, גיל). כמו כן, מקובל גם להבחין בין תכונות בדידות בעלות אוסף סט סופי ובר מנייה של ערכים (מס’ סטודנטים הרשומים בקורס), בין תכונות רציפות בעלות ערכים מספריים ממשיים (משקל), לבין תכונות בינריות שהן מקרה פרטי של של תכונות בדידות, והן בעלות שני ערכים (0 או 1, זכר או נקבה, נכון או לא נכון).

**דוגמה ל Bining – חלוקה לקטעים**
ל Bining יש 2 גישות. הגישה הראשונה היא ‘רוחב שווה’ ז”א אותה רוחב לכל קטע/תיבה (ע”י [ערך המקסימום-ערך המינמום] / מס’ הערכים הכולל). אך לגישה זו יש חסרון מהותי, שכן אם ערך המקסימום או המינימום יושפעו כתוצאה מערך חריג (הנגרם מרעש), אותו ערך חריג ישפיע על הממוצע השייך לכל הערכים באותו קטע. כדי להתגבר על הבעיה הזו, בגישה השניה, הנקראת ‘עומק שווה’, אנו לא מחוייבים לאותו מס’ של ערכים בכל קטע/תיבה (אך אנו בכל זאת מקפידים פחות או יותר על מספר שווה).

ערכים לדוגמה: 4,8,9,15,21,21,24,25,26,28,29,34.

נחלק את הערכים לקטעים/תיבות:

* תיבה 1: 4, 8, 9, 15
* תיבה 2: 21, 21, 24, 25
* תיבה 3: 26, 28, 29, 34

אם נבצע Bining על פי הגישה השניה (עומק שווה):

* תיבה 1: 9, 9, 9, 9
* תיבה 2: 23, 23, 23, 23
* תיבה 3: 29, 29, 29, 29

Bining על פי הגישה הראשונה (רוחב שווה):

* תיבה 1: 4, 4, 4, 15
* תיבה 2: 21, 21, 25, 25
* תיבה 3: 26, 26, 26, 34

 &nbsp;

### 4. שילוב והמרות של נתונים
 כדי לשפר את היעילות, נעביר את הנתונים לתבנית המיועדת לכריית מידע. תבנית זאת תיהיה קטנה יותר ומייצגת, וקיימות מגוון שיטות לבצע זאת:

* החלקת הנתונים – הסרת רעשים,
* קיבוץ הנתונים – לדוגמה, אם אנו מעוניינים בהיקף הרשמות חודשיות לקורסים באונ’. אז נקבץ את נתוני ההרשמות היומיות לנתון מסכם חודשי.
* הכללת הנתונים.
* מימדים – הפחתה בתכונות.
* דחיסת נתונים.
* דיסקרטיזציה – העברת נתונים נומריים (רציפים) לערכים קטגוריאליים. אפשר להשתמש בנירמול שבין min ל max, בעזרת הנוסחה הבאה: `v’ ={[v – minA] / [maxA – minA]} * (new_maxA – new_minA) + new_minA (כאשר new_minA` ו new_maxA הם ערכי ה max וה min החדשים). או בשימוש בנירמול המבוסס על ההתפלגות הנורמלית (z-score), בעזרת הנוסחה: `v’ = (v – uA) / σA` (כאשר u הוא הממוצע-תוחלת, ו σ הוא סטיית התקן).

 &nbsp;

### 5. סיווג vs וחיזוי
בבעיית הסיווג (classification) אנו מעוניינים לנבא משתנים קטגוריים (בדידים או נומינליים). הדוגמה הפשוטה ביותר למשתנה כזה היא משתנה בינארי (בעל 2 אפשרויות בלבד), לדוגמה הצלחה בקורס לעומת כישלון בקורס, זכר או נקבה, נכון או לא נכון וכד’. אך משתנים קטגוריים יכולים להיות בעלי יותר מ-2 משתנים. לעומתה בבעיית החיזוי (prediction) אנו מעוניינים לנבא ערכים של משתנים רציפים (כמו ציון של סטודנט בקורס, שער של מטבע וכד’). בעיות טיפוסיות של סיווג יכולות להיות אישור מתן אשראי (אושר / נדחה), אבחון רפואי (צהבת B / צהבת C) וכד’, ומנגד בעיות טיפוסיות של חיזוי הן, לדוגמה, חיזוי תוחלת החיים בביטוח רפואי, חיזוי ממוצע ציוני הסטודנט בקבלה לאונ’ וכד’.

**תהליך הסיווג**
תהליך הסיווג מורכב מ-2 שלבים, בשלב בהראשון אנחנו בונים את המודל שאמור לספק תיאור למספר סיווגים שידועים לנו מראש. בשלב בניית המודל אנו משתמשים בתצפיות אימון (ז”א שאנו מחלקים את מאגר המידע שלנו ל-3, כאשר אנו יודעים את הסיווג של 2 החלקים הראשונים. נשתמש בחלק הראשון לבניית המודל, ובחלק השני (שאנו יודעים את הסיווג שלו) לבחינת המודל שבנינו. במידה והמודל תקין וקיבלנו אותו, נשתמש בו על החלק השלישי כדי לנבא (את הסיווג של החלק השלישי אנו לא יודעים, וזה מה שאנו מנבאים)). את המודל ניתן לייצג באמצעות חוקי סיווג, עצי החלטה, או איזשהן נוסחאות מתמטיות.

התנאי הבסיסי לקבלת המודל סיווג שבנינו הוא שיהיה מדוייק יותר מ[חוק הרוב](http://en.wikipedia.org/wiki/Majority_rule) (לדוגמה, נניח שאנו רוצים לקבל תחזית האם מחר ירד או לא גשם, נניח גם שב-90% מהמקרים לא יורד גשם. מכאן אנו יכולים לבנות מודל פשוט האומר שמחר לא ירד גשם, וזאת עפ”י ההנחה שרק ב-10% מהמקרים יורד גשם. לכן מודל כזה, שנבא את מזג האוויר ללא קשר, יהיה מדוייק ב-90% מהמקרים).

 דוגמה לבניית מודל הסיווג: נניח שקיבלנו את ה Training Data (החלק הראשון של המאגר, ממנו אנו בונים את המודל) הבא:

| Tenured | Years | Rank | Name |
|:---:|:---:|:---:|:---:|
|no | 3 | Assistant Prof | Mike |
|yes | 7 | Assistant Prof | Mary |
|yes | 2 | Professor | Bill |
|yes | 7 | Associate Prof | Jim |
|no | 6 | Assistant Prof | Dave |
|no | 3 | Associate Prof | Anne |

ואנו רוצים לנבא את הערך Tenured, ז”א לנבא אם לפרופ’ יש קביעות. ניתן לראות שחוק הרוב במקרה הזה הוא 50% (3 אנשים yes ו-3 no). נרחיב בעתיד כיצד אנו מגיעים למודל, אך מהר מאוד ניתן לראות שחוק כמו `‘IF rank = ‘professor’ OR years > 6 THEN tenured = ‘yes` יתן לנו דיוק של 100% על תצפיות האימון (כל התצפיות מקיימות אותו). אך זה שהמודל נותן לנו 100% דיוק על תצפיות האימון אין זה אומר שזהו המודל שאנו רוצים מכיוון שאין זה אומר שהוא יתן לנו 100% דיוק על תצפיות המבחן (החלק ה-2 במאגר המידע, שאנו יודעים את הסיווג של התצפיות אך משתמשים בהם לבחינת המודל). אם מתקיים מצב כזה (בו המודל מתאים ב-100% לתצפיות האימון אך לא לתצפיות המבחן מתקיימת בעיית הנקראת המתאמת יתר ([Overfitting](http://en.wikipedia.org/wiki/Overfitting)) עליה נרחיב בהמשך.

בשלב ה-2 של תהליך הסיווג אנו משתמשים בחלק ה-2 של מאגר התצפיות, ה Testing Data, להלן התצפיות:

| Tenured | Years | Rank | Name |
|:---:|:---:|:---:|:---:|
| no | 2 | Assistant Prof | Tom |
| no | 7 | Associate Prof | Merlisa |
| yes | 5 | Professor | George |
| yes | 7 | Assistant Prof | Joseph |

נפעיל את החוק, ונראה כי החוק אינו מקיים את התצפיות הראשונה (של Tom), מקיים את התצפית השנייה (של Merlisa) בגלל שהוותק שלה גדול מ-6, וזאת הרי טעות (כפי שניתן לראות אין לה קביעות). את התצפית השלישית (של George) והרביעית (של Joseph) החוק מקיים, ואכן יש להם קביעות. לכן, מצאנו חוק שפחות מדויק על נתוני המבחן מאשר על נתוני האימון, אך יותר מדויק מחוק הרוב (המודל שלנו מדוייק ב-75%). ולכן אפשר לשאול האם ההבדל הוא מובהק (מבחינה סטטיסטית) או הבדל שהתקבל באופן מקרי מתוך הנתונים? נחזור לשאלה זאת בהמשך.

נציין כי עבור בסיס נתונים גדול מקובל לחלק את הנתונים כך ש-2/3 יהוו נתוני אימון, ו-1/3 יהיו נתוני מבחן. עבור בסיס נתונים בינוני, מקובל לחלק את הנתונים ל-k תת דגימות, כאשר (k-1) תת דגימות יהוו נתוני אימון ותת דגימה אחת תהווה נתוני מבחן. ועבור בסיס נתונים קטן, מקובל להשתמש בגישת Bootstrap עליה לא נרחיב.

  &nbsp;

### 6. מדדים להערכת דיוק ושגיאה
בשלב זה, לאחר שבנינו את מודל הסיווג, המטרה שלנו היא להעריך את אחוז הדיוק של המודל, ונעריך אותו תוך התייחסות לנושאים כגון דיוק הסיווג, מהירות וסקלביליות (יכולת המסווג להתמודד עם כמות גדולה של נתונים), שרידות ועמידות (טיפול בנתונים רועשים וערכים חסרים), יכולת אינטרפרטציה, וכ’ו.

ניקח לדוג’ את בסיס הנתונים לרכישת מחשב, לאחר שהפעלנו עליו מודל סיווג, ונייצג את הנתונים באמצעות מטריצת הצלבה (confusion matrix) (השורה הראשונה היא החיזוי של המודל שלנו על נתוני המבחן, והעמודה הראשונה היא התוצאות האמיתיות של נתוני המבחן):

| --- | --- | --- | --- | Predicted class |
|:---:|:---:|:---:|:---:|:---:|
| recognition (%) | total | buy_computer = no | 	buy_computer = yes | Actual class |
| 99.34 | 7000 | 46 | 6954 | buy_computer = yes |
| 86.27 | 3000 | 2588 | 412 | buy_computer = no |
| 95.42 | 10000 | 2634 | 7366 | total |

**אחוז השגיאה** (Error rate) של המודל, הוא `1 פחות גובה הדיוק [(acc(M]` (כלומר, במקרה שלנו 412+46 מתוך 10,000 נגיע לאחוז השגיאה של המודל).

בנוסף למדד אחוז השגיאה ולגובה הדיוק, ישנם מדדים נוספים:

* **מדד הרגישות (sensitivity)** – המחושב ע”י TP/P (ראשי תיבות של True_Positive ז”א אלה שהמודל צפה שיקנו מחשב ובאמת קנו מחשב, חלקי Positive ז”א כל אלה שבאמת רכשו מחשב). במקרה שלנו אנו נחשב 6954/7000 ונקבל 99.34%.
* **מדד הייחודיות (specificity)** – המחשוב ע”י TN/N (ראשי תיבות של True_Negative ז”א אלה שהמודל צפה שלא יקנו מחשב ובאמת לא קנו מחשב, חלקי Negative ז”א כל אלה שבאמת לא קנו מחשב). במקרה שלנו אנו נחשב 2588/3000 ונקבל 86.27%.
*  **מדד המדויקות (precision)** – המחשוב ע”י (TP/(TP+FP (ראשי תיבות של True_Positive, חלקי True_Positive ועוד False_Positive – אלה שהמודל שלנו צפה שיקנו מחשב אך לא באמת קנו מחשב). במקרה שלנו אנו נחשב (6954+412)/6954 ונקבל 94.41%.
* **מדד הדיוק (accuracy)** – המחשוב ע”י (TP+TN)/(P+N), ז”א True_Positive ועוד True_Negative חלקי כל ה Positive ועוד כל ה Negative. במקרה שלנו אנו נחשב (10000)/(6954+2588) ונקבל 95.42%.


**עקומת ROC**
ROC אלה ראשי תיבות של [Receiver Operating Characteristics](http://en.wikipedia.org/wiki/Receiver_operating_characteristic) (עקומות מאפייני קליטה והפעלה), ונשתמש בהם על מנת לבצע השוואה וויזואלית בין מודלים שונים לסיווג. כמו שניתן לראות בתמונה משמאל, העקומה מציגה את הקשר / תמורה בין אחוז ה True Positive ל False Positive.

כדי לבנות את העקומה אנו לוקחים את תצפיות המבחן ומדרגים אותם עפ”י סדר יורד של ההסתברות להשתייך לסיווג החיובי. כלומר, אם נסתכל על ציר ה x של עקומת ה ROC אנו נראה בצד שמאל (של ציר ה x) את התצפיות בעלות הסיכוי הגבוהה יותר להיות תצפיות חיוביות עפ”י המודל. במקרה הטוב ביותר נקבל עקומה הקרובה מאוד לזווית ישירה, ושטח של 1 (מתחת לעקומה), אך אם המודל אינו משפר את הדיוק (מודל אקראי – אין קשר בינו לבין המציאות) נקבל את כל התצפיות על קו האמצע (קו 45 מעלות), ובמקרה זה אין הבדל באחוז השגיאה לגבי תצפיות בעלות סיכוי גבוהה להשתייך לסיווג החיובי לעומת תצפיות שיש להן סיכוי נמוך להשתייך לאותו סיווג.

 ככל שהעקומת ה ROC שלנו תעלה מעל קו 45 מעלות ותתקרב לזווית הישרה, כך נקבל שטח גדול יותר (מתחת לקו), וזה אומר שהמודל שלנו יהיה מדוייק יותר.

**מדדים להערכת החיזוי**
בבעיות חיזוי אנו מנסים לנבא ערך של משתנה רציף, ובדרך כלל הערך החזוי יהיה שונה מהערך בפועל, ואז אנו בודקים את המרחק (או ההפרש) בין שתי הערכים. קיימות 2 פונקציות הפסד: הראשונה היא ‘שגיאה אבסולוטית’ (Absolute Error) ומחושבת כ `|’yi – yi|` (כאשר yi הוא הערך בפועל, ו ‘yi הוא הערך החזוי), והפונ’ השניה היא ‘שגיאה ריבועית’ ומחושבת כ 2^(‘yi – yi). שימו לב כי 2 הפונ’ מבטלות ערכים שליליים, ז”א שאין הבדל אם הערך החזוי קטן מהערך בפועל או להפך. להלן דוגמה לחישוב מודל חיזוי הנבא היקף מכירות בפועל כתלות בהשקעה בפרסומות:

| השקעה בפרסומת | היקף מכירות בפועל | היקף מכירות חזוי | Abs. Error	Sq. Error |
|:---:|:---:|:---:|:---:|
| 1 | 5 | 5.6 | 0.6 | 0.36|
| 2 | 8 | 7.2 | 0.8 | 0.64 |
| 3 | 9 | 8.8 | 0.2 | 0.04 |
| 4 | 10 | 10.4 | 0.4 | 0.16 |


בהינתן מאגר תצפיות מסויים, נשאלת השאלה כיצד נעריך את הדיוק של המסווג או של מודל החיזוי?

הגישה הפשוטה ביותר לביצוע הערכה כזאת נקראת Holdout method, ובה יש לקחת את כל התצפיות העומדות לרשותנו (אלה שאנו כן יודעים את הסיווג או את הערך של משתנה המטרה) ומחלקים ל-2 קבוצות. קבוצה ראשונה היא קבוצת האימון (לדוג’ לקחת 2/3 מהתצפיות לקב’ האימון ולהשתמש בהם על מנת לבנות את המודל) יתר התצפיות ישמשו כקבוצת המבחן (לדוג’ אם לקחנו 2/3 לקב’ האימון, ניקח את ה1/3 הנותרים כקבוצת המבחן ונעריך את דיוק המודל על פיהם) (כמובן שהחלוקה צריכה להיות אקראית).

שיטה מתקדמת יותר (וגם מקובלת יותר) נקראת Cross Validation (או k-fold), ובה לוקחים את כל התצפיות ומחלקים אותם ל k קבוצות שונות (כשה k המקובל הוא 10), פחות או יותר שוות. לאחר החלוקה, מריצים את האלגוריתם k פעמים, כאשר בכל איטרציה, i, קבוצה מס’ i משמשת כקבוצת המבחן, ושאר התצפיות הנמצאות ביתר הקבוצות משמשות כקבוצת האימון. שיטה זה מדוייקת יותר מ Holdout ולכן גם מקובלת יותר.

 &nbsp;

### 7. שיטות חיזוי (מודלים של רגרסיה)
**רגרסיה לינארית**
המטרה של המודל היא לחזות ערכים של משתנה y על סמך של משתנה אחר (x) (כאשר הוא מניח ש-2 המשתנים רציפים). המודל הינו מסוג `Yi = a + bXi + ei` (כאשר e היא טעות אקראית), להלן דוגמה: Y=5 + 2x +e. בתמונה משמאל ניתן לראות דוגמה לשימוש במודל רגרסיה לינארית פשוטה, כאשר הקו מייצג את הפולינום והנק’ מייצגות את הנתונים האמיתיים.

המרכיבים של המודל הם:

* X ו Y – כש X הוא המשתנה הבלתי תלוי, הגורם שאנו מניחים שמשפיע על המשתנה התלוי, Y.
* a – השיפוע (קבוע).
* b – מקדם הרגרסיה.
* e – השגיאה אקראית (אנו מניחים שישנם גורמים נוספים – לא ידועים לנו, ואנו מביאים אותם לידי ביטוי בשגיאה האקראית).

ברגרסיה, כאמור, יש לנו את Yi שהוא הערך האמיתי, ואת ‘Yi שאותו אנו מחשבים כפי שאמרנו [Yi = a + bXi]. ומטרתנו היא להעריך את הפרמטרים של קו הרגרסיה, a ו b. הגישה המקובלת ביותר לחישוב מקדמי הרגרסיה נקראת גישת הריבועים הפחותים (Least Squares Approach) וניתן לראות את 2 הנוסחאות שלה בתמונה משמאל. עפ”י גישה זו אנו רוצים להביא למינימום את סכום ריבועי השגיאה בין הרגרסיה לנקודות בפועל. רוב התוכנות הסטטיסטיות היום (כולל Excel) יודעות כבר לבצע את החישובים האלו, כך שלא נצטרך לבצע את העבודה השחורה בעצמנו.

את מודל הרגרסיה הלינארית ניתן להרחיב לכמה משתנים (נקרא גם רגרסיה לינארית רבת משתנים – Multiple Linear Regression), במודל זה השיפוע a נשאר יחיד, אך כל אחד מהמשתנים x1, x2 ועד xk מקבל מקדם b משלו (ז”א b1, b2 עד bk), וגם השגיאה האקראית (בעלת ההתפלגות הנורמלית) נשארת (יחידה).

בנוסף לרגרסיה לינארית פשוטה ורגרסיה לינארית רבת משתנים, ישנם גם מודלים לרגרסיה לא לינארית, וזאת מכיוון שלפעמים צורת הנתונים אינה מתאימה למודל לינארי כזה או אחר, אלא לדוג’ למודל פולינומי הנראה כך: y = w0 + w1*x + w2*x^2 + w3*x^3 ואנו מחשבים אותה ע”י הפיכה של x במעלה למשתנה בפני עצמו (ז”א x2 = x^2).



**דוגמה לרגרסיה לינארית**

ניקח לדוגמה את הטבלה של ציוני הקורס “מבוא למדעי המחשב” המרוכבת מציון משוקלל של המטלות ומציון הבחינה.

| I | Xi ציון מטלות משוקלל | Yi ציון בחינה |
|:---:|:---:|:---:|
| 1 | 88 | 87 |
| 2 | 63 | 37 |
| 3 | 47 | 53 |
| 4 | 93 | 78 |
| 5 | 73 | 61 |
| 6 | 56 | 62 |
| 7 | 42 | 29 |
| 8 | 82 | 91 |
| 9 | 59 | 68 |
| 10 | 65 | 52 |

נחשב עפ”י הנוסחאות של רגרסיה לינארית (למעלה בתמונות משמאל) את b גג: 2607.6 / 2446.6 שיהיה שווה ל 0.938. ולאחר מכן נציב בנוסחה השניה כדי למצוא את a גג: 66.8*0.938 – 61.8 ששווה ל 0.858-.

לאחר מכן נתאר את קו הריבועים הפחותים (קו הרגריסה), ז”א Y גג. Ý = -0.858 + 0.938X.

ובשלב השלישי נחשב את טעות הניבוי: נתבונן בציון המטלות המשוקלל של הסטודנט הראשון. אזי ציון הבחינה המנובא על סמך קו הריבועים הפחותים שמצאנו בשלב 2 יהיה 88*Ý1 = -0.858 + 0.938X ז”א 81.686. לכן טעות הניבוי היא ê1 = 87 – 81.686 = 5.314.



**דוגמה נוספת:**

בדוגמה השניה אנו לוקחים קובץ DB קטן ([מכאן](http://archive.ics.uci.edu/ml/datasets/Lenses)) בו ישנם נתוני עדשות מגע. מכיוון שאנו עובדים עם תוכנת ה Weka והקובץ אינו בדיוק מוכן בשבילה, סידרתי אותו ושמרתי ב csv, תוכלו להוריד אותו [מכאן](http://www.lifelongstudent.io/files/lenses.csv) (את Weka ניתן להוריד [מכאן](http://www.cs.waikato.ac.nz/ml/weka/)). נפתח את תוכנת ה Weka ויפתח לנו חלון קטן (כמו בתמונה מצד שמאל) בו נלחץ על Explorer ונראה את החלון הגדול הבא:

לאחר שנטען את הקובץ נעבור לחוצץ Classify ונלחץ על Choose כדי לבחור את המודל, בחלון שיפתח לנו נבחר בתייקיה functions ובפונ’ SimpleLinearRegression. לאחר מכן תחת Test Options נבחר ב Cross-validation Folds ונכתוב בתיבה 10 (בשיטה זו כל התצפיות מחולקות ל k קבוצות שונות, במקרה שלנו בחרנו 10, כשכל תצפית שייכת אך ורק לקבוצה אחת מתוך ה k. ומריצים את האלגו’ k פעמים, כאשר בהרצה מספר i, תת קבוצה Di משמשת כקבוצת המבחן והשאר כקבוצות האימון). ונלחץ על Start:


ניתן לראות (סימנתי בתמונה בריבוע אדום) שקחבךנן את קו הריבועים הפחותים (קו הרגריסה).

&nbsp;

### 8. השוואה בין מסווגים
בהינתן נתונים עם תוויות של סיווג, אנחנו יכולים להריץ אלגו’ שונים ולקבל מגוון תוצאות של דיוק. נשאלת השאלה איך מחליטים שאלגו’ מסויים הוא טוב יותר מהשני (מספק דיוק גבוהה יותר, או לחילופין שגיאה נמוכה יותר).

רווח בר סמך ([Confidence Intervals](http://en.wikipedia.org/wiki/Confidence_interval)) מספק את התחום המשוער של הערכים שבו, קרוב לוודאי, נמצא פרמטר לא ידוע של האוכלוסיה. למשל כאשר ישנו רווח בר סמך ברמת מובהקות של 95% אנו יודעים שאם נבנה הרבה מאוד אינטרוולים כאלה, 95% מהם יכילו את הפרמטר הלא ידוע של האוכלוסיה. נציין גם שניתן לבנות הרבה מאוד סוגים של רווחי בר סמך ברמות מובהקות שונות, אך מקובל לבנות ב 90%, 95%, ו 99%.

על מנת לבנות רווח בר סמך עבור השגיאה, נפעיל את המודל על תצפיות השגיאה ונקבל שגיאה משוערת (נקרא גם Err Test (מלשון Error)). לאחר מכן אנו מניחים שהתפלגות השגיאה היא בינומית, ובמידה ויש לנו יותר מ-30 תצפיות (שקיבלנו באופן מקרי ובלתי תלוי מהאוכלוסיה) אנו מקרבים אותה להתפלגות הנורמלית. במידה ואנו נשתמש בקירוב להתפלגות הנורמלית נשתמש בנוסחה שבתמונה מצד שמאל (כאשר Z אלפא הוא מספר סטיות התקן של ההתפלגות הנורמלית).

 בטבלה משמאל ניתן לראות דוגמה לחישוב הרווח בר סמך. כאשר למודל הראשון יש שגיאה של 0.2 (Error1) כשגודל קב’ המבחן הוא 30 תצפיות, ולמודל השני יש שגיאה של 0.4 (Error2) כשגודל קב’ המבחן הוא 40 תצפיות. נחשב את רווח הבר סמך עבור ערכים שונים של alpha (הראשון 0.01 ז”א רמת מובהקות של , השני 0.05, והשלישי 0.1).

נשים לב שעבור ה alpha ששווה ל-0.01 אנחנו מקבלים רווח בר סמך שהגבול התחתון שלו נמצא מתחת לאפס, וזה אומר שעבור אותו ערך של אלפא אנחנו לא בטוחים מי מבין המסווגים מדוויק יותר. עבור ערכי אלפא האחרים ניתן בהחלט לטעון כי המסווג השני הוא טוב יותר מהראשון (כי השגיאה שלו נמוכה יותר מהשגיאה של המסווג הראשון באופן מובהק).

&nbsp;

### סיכום
בפוסט הזה דיברנו על נושא חשוב הנקרא עיבוד מוקדם של נתונים (תהליך חשוב במיוחד עבור האלגוריתמים של כריית מידע). ראינו גם ייצוג גרפי של נתונים העוזר לנו להבין את הקשרים בין הנתונים, וללמוד על ההתפלגות שלהם. הפעולות העקריות של עיבוד הנתונים כוללות: ניקוי ושילוב הנתונים, בחירת משתנים, וחלוקה ל Binים (תיבות). בנוסף, דיברנו על בעיות סיווג ובעיות חיזוי וכיצד נעריך את הדיוק והשגיאה של מודלים אלו, הסברנו מהי עקומת ROC ומה השימוש שלה, ודיברנו על סוגים שונים של רגרסיה (לינאראית, רבת משתנים, לא לינארית) למרות שלא נצטרך לחשב אותה לבד. ולבסוף הראנו כיצד להשוות בין מסווגים שונים (בעזרת רווח בר סמך).

אני שמח להגיד שעד כאן נגמר החלק ה”משעמם” ומתחיל החלק המעניין יותר, מפה והלאה לא רק נציג ונסביר מושגים ושיטות אלה נבנה אלגו’ לכריית מידע, ונבין כיצד הם פועלים. האלגו’ הראשון עליו נדבר כבר בפוסט הבא הוא “עצי החלטה”.