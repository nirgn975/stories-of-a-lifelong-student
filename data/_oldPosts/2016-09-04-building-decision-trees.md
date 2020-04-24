---
title: בניית עצי החלטה
author: nirgn
layout: post
summary: ""
category: Data Mining
---
בפוסט הזה נבצע סקירה כללית של עצי החלטה ובנייתם, נדבר על האלגוריתם הבסיסי לבניית עצי החלטה (הנקרא ID3), נדבר על שיטות למניעת בעיית התאמת יתר (overfitting), נראה כיצד להפיק חוקים בתוך עצי החלטה, ונתייחס לשיטות דיסקרטיזציה חלוקה למרווחים של משתנים רציפים (תוך כדי בניה של עצי החלטה).

סקירה של בניית עצי החלטה
אלגוריתם לבניית עצי החלטה - ID3
התאמת יתר (Overfitting)
הפקת חוקים מעץ החלטה וחלוקה למרווחים
דשכג


1. סקירה של בניית עצי החלטה

עצי החלטה היא טכניקת סיווג. נזכיר כי סיווג היא מתודולוגיה שמטרתה לחזות תוויות סיווג קטגוריות עבור נתונים חדשים, אותם תוויות סיווג קטגוריות יכולות להיות במקרה של סיווג ביטחוני עבר / נכשל, במקרה של נטישת לקוחות (תחום שמעניין במיוחד חברות סלולר) עובר חברה / נשאר, במקרה של תחזית מזג האוויר שמשי / מעונן / גשום וכד'. את הנושאים השונים בפוסט נראה על דוגמה קבועה בה אנו מתבקשים לקבוע קבלה של סטודנטים לאונ'. אנו נסתמך על נתונים קודמים כדי לקבוע את ממוצע התואר של הסטודנטים המבקשים להתקבל. להלן הנתונים ההיסטורים:

GPA	Graduation Year	Test Grade	Admission Year	Place of Birth	Date of Birth	Gender	Last Name	First Name	ID
93.5	2006	730	2002	USA	18/12/1979	M	Cohen	David	543406619
87.5	2006	680	2002	Israel	11/07/1980	M	Levy	Ophir	951984264
94.3	2006	640	2002	Israel	19/05/1981	F	Grosman	Sharon	683168092
85.8	2006	585	2002	Russia	11/02/1980	F	Liberman	Diana	100900927
78.7	2006	570	2002	Israel	03/02/1982	F	Klein	Anat	516120403
ולהלן הנתונים של הסטודנטים המבקשים להתקבל:

GPA	Graduation Year	Test Grade	Admission Year	Place of Birth	Date of Birth	Gender	Last Name	First Name	ID
?	2011	580	2007	Israel	22/09/1985	M	Bazak	Boaz	537793401
?	2011	650	2007	Israel	10/02/1985	M	Levy	Ophir	808943728
?	2011	720	2007	Ukraine	03/12/1987	F	Neuman	Maria	537362102
כמו שניתן לראות ה GPA אינו ידוע (זה מה שאנו צריכים לחזות), ותתעלמו מהשנים, נניח כי אנחנו בתחילת שנת 2007 ואלו הנתונים של הסטודנטים המבקשים להיכנס לאונ' בשנת הלימודים הבאה.



לפני שנתחיל להפעיל אלגוריתם כזה או אחר של כריית מידע, נבצע ניקוי של נתונים שאינם רלוונטים לתהליך החיזוי (בדיוק כמו שדיברנו בפוסט הראשון). נתחיל בלהוציא את מספר תעודת הזהות, ברור לנו שלת.ז. אין השפעה להצלחה בלימודים. לאחר מכן נתעלם מהשם הפרטי והמשפחה, מכיוון שגם זה לא עוזר לנו בחיזוי ההצלחה בלימודים. לאחר מכן ניפטר גם מתאריך הלידה, קודם במסד נתונים גדול יהיו הרבה מאוד ערכים אפשריים, דבר שמקשה על החיזוי ובעצם יפרק לנו את העץ ליותר מידי עלים, לאחר מכן ישנה הטענה כי אם אנו בכלל לא מאמינים באסטרולוגיה תאריך הלידה לא משפיע על ההצלחה בלימודים, ומכיוון שאני לא מאמין באסטרולוגיה נסיר את העמודה. ולבסוף, גם את עמודת שנת הקבלה ושנת הסיום אפשר להסיר מכיוון שאינם עמודות משמעותיות ואף זהות לכל התצפיות במקרה שלנו.

נשארנו עם טבלה מצומצת המכילה 3 עמודות בלבד (GPA לא נחשב, זהו משתנה המטרה שלנו):

GPA	Test Grade	Place of Birth	Gender
93.5	730	USA	M
87.5	680	Israel	M
94.3	640	Israel	F
85.8	585	Russia	F
78.7	570	Israel	F
לאחר שזיהנו את המשתנים הפוטנציאלים ואת משתנה המטרה, נשאלת השאלה כיצד נמצא את המודל הטוב ביותר לחיזוי ממוצע הציונים (GPA)? וכיצד נקבע אם מודל מסויים הוא טוב יותר או פחות ממודל אחר? כרגע, לצורך המשך הדיון, נניח כי המודל הטוב ביותר הוא המודל המדויק ביותר (זה שמאפשר לנו לחזות בדיוק המריבי מה יהיה הציון הממוצע בסיום לימודיו).



Test Grade Line Fit Plotהדבר הראשון שניתן לעשות הוא לנסות לחזות באמצעות רגרסיה לינארית פשוטה או רבת משתנים, במקרה הזה ננסה עם רגרסיה לינארית פשוטה עם משתנה הפסיכומטרי (Test Grade) ונראה (בתמונה משמאל) כי רוב המשתנים במציאות די רחוקים מקו הרגרסיה שהתקבל. אנו גם יכולים להעריך את  איכות המודל הזה בדרך יותר פורמלית, תוך שימוש במקדם המתאם R^2 (דיברנו עליו בפוסט הקודם, מדד שמקבל ערכים בין 0 [אין קשר בין המשתנים] ל-1 [יש קשר מוחלט בין המשתנים]) ויוצא במקרה הזה כי R^2 = 0.547 שזה מקדם מתאם יחסית נמוך, כלומר אם נשתמש ברגריסה לינארית פשוטה על מנת לנבא ציון ממוצע באונ' נקבל מודל בעל דיוק לא גבוהה. מכאן אנו מסיקים כי הקשר בין המשתנים לממוצע הציונים אינו קשר לינארי פשוט, אלא קשר מורכב יותר.



נשים לב לעוד בעיה בבנושא ניקוי הנתונים, והיא ריבוי ערכים. במקרה שלנו אנו רואים שהבעיה הזאת קיימת במקום לידה (Place of Birth), נתון זה יכול לקבל הרבה מאוד ערכים אפשריים, מצב שאינו טוב לעצי החלטה (מסיבות שעליהם נדבר בהמשך), ולכן נחליף את הערכים האפשריים בעמודה ל-2 בלבד: Israel ו Abroad. משתנה נוסף שנצמצם את מספר הערכים שלו (נבצע לו דיסקרטיזציה) הוא ציון הפסיכומטרי (Test Grade), נקבע באופן שרירותי (כרגע) 3 מרווחים (אינטרוולים): ציונים בין 0 ל-600, ציונים בין 600 ל-700, וציונים מעל 700 (בהמשך נדבר על שיטות למציאה של מספר מרווחים והגבולות שלהם בצורה האופטימלית ביותר). ולבסוף, נבצע דיסקרטיזציה גם למשתנה המטרה, גם כאן באופן שרירותי ל-3 מרווחים: Low שיהיה 0-79, Meduim שיהיה בין 80-89, ו Hight שיהיה 90-100.

לפעולת הדיסקרטיזציה יש משמעות מיוחדת - אנו מניחים מראש שיהיה לנו קשה מאוד למצוא מודל שמצליח לחזות באופן מדויק את הממוצע של כל מועמד (ואף להניח שאין לנו צורך במודל כזה, שכן לצורך קבלה לאונ' סביר להניח שיש להניח בחיזוי מקורב יותר - כזה שמדבר על מרווחים ולא על ציונים מדוייקים). לאחר שביצענו את כל פעולות ההכנה כך נראת טבלת הנתונים:

GPA	Test Grade	Place of Birth	Date of Birth	Gender	Last Name	First Name
High	Over 700	Abroad	18/12/1979	M	Cohen	David
Medium	600-700	Israel	11/07/1980	M	Levy	Ophir
High	600-700	Israel	19/05/1981	F	Grosman	Sharon
Medium	0-600	Abroad	11/02/1980	F	Liberman	Diana
Low	0-600	Israel	03/02/1982	F	Klein	Anat
Target	Input	Input	Input	Input	Use:	 
Predict GPA Treeכעת (עוד לפני שדיברנו על איזשהו אלגו' לבניית עצי החלטה) ננסה לבנות בעצמנו עץ החלטה, בעזרת אחד מהמשתנים המנבים. ניקח את ציון הפסיכומטרי - Test Grade, ואנו יודעים שיש לו 3 ערכים אפשריים ועל פיהם נשייך את התצפיות לענפים המתאימים. העץ שנקבל נמצא בתמונה משמאל.

נשים לב שאם נבצע חיזוי עבור כל אחד משלושת הענפים, לגבי הענף השמאלי אין לנו החלטה חד משמעית (יש לנו שם תוצאה של Low וגם של Medium ז"א 50% שנקבל כל אחת מהתוצאות האלו), גם בענף האמצעי אנחנו לא יכולים להגיע להחלטה חד משמעית, ורק בענף הימני (Over 700) אנחנו יכולים להגיע להחלטה חד משמעית בה החיזוי יהיה ציון ממוצע של High. מכאן הדיוק הממוצע של העץ הוא 60% [מחשבים אותו כך: 100%*(1/5) + 50%*(2/5)  + 50%*(2/5)].

Predict  GPA Tree by Gender



כמובן שלא חייבים להשתמש במשתנה ציון פסיכומטרי, ניתן להשתמש גם באחרים, לדוג' בתמונה משמאל ישנו עץ החלטה שנבנה עפ"י המשתנה מין (Gender). בענף השמאלי אין לנו החלטה חד משמעית, ודיוק של הענף הוא 33% בלבד, בעוד בענף הימני הדיוק 50%. מכאן שהדיוק הממוצע של העץ הוא 40% בלבד [מחושב: 50%*(2/5) + 33%*(3/5)].

Predict GPA Tree by Place of Birth



ישנו משתנה נוסף לפיו אנו יכולים להרכיב עץ החלטה והוא מקום לידה. עפ"י משתנה זה, עבור אלה שנולדו בארץ (הענף השמאלי - Israel) יש לנו דיוק של 33%, ועבור אלה שנולדו בחול (הענף הימני - Abroad) יש לנו דיוק של 50%. ולכן גם כאן אנחנו מגיעים לדיוק ממוצע של 40%.



אחרי שבנינו 3 עצים אפשריים, אנחנו מתלבטים באיזה משתנה להשתמש בשורש העץ? לפני שנתאר את האלגוריתם שיעזור לנו לענות על השאלה הזאת, נעשה יישור קו קטן ונעבור על כמה מושגים הקשורים למבנה העץ.

קודקוד (Nodes) - יכול להיות קודקוד השורש, אך גם להופיע באחת הרמות הבאות של עץ ההחלטה. המאפיין שלו הוא שהוא מתייחס לבדיקת הערכים של אחד המשתנים.
ענף (Branch) - כל אחד מהערכים של אותו משתנה הנבדק בקודקוד יוצר איזשהו ענף של העץ.
עלה (Leaf) - כאשר אנו מגיעים לעלה אנו מפסיקים לפצל את העלה למשתנים אחרים ונותנים תחזית, ניבוי לסיווג של משתנה המטרה.
מסלול (Path) - המסלול מחבר בין קודקוד השורש לאחד העלים. כפי שנראה בהמשך, כל מסלול כזה מייצג חוק חיזוי. ונשים לב כי מס' המסלולים בעץ ההחלטה יהיה תמיד שווה למס' העלים.
סוגי תכונות ואופי הפיצול המתאימים להם:

תכונות בינאריות (Binary Attributes) - יפוצלו פיצול בינארי בלבד (כן / לא).
תכונות נומינליות (Nominal Attributes) - לכל תכונה יש מספר קטן וסופי של ערכים אפשריים. ניתן לפצל תכונות נומינליות לפיצול מרובה (multiway split) וכפיצול בינארי (binary split) (בפיצול בינארי יש לקבל יחד כמה ערכי תכונה). לדוגמה: סטטוס סטודנט עשוי להיות סטודנט פעיל / שהפסיק את לימודיו / סטודנט חדש. פיצול מרובה יהיה פיצול ל-3 התכונות, בעוד פיצול בינארי יכול להיות {הפסיק לימודיו} / {פעיל, חדש} או {פעיל, הפסיק לימודיו} / {חדש} או {חדש, הפסיק לימודיו} / פעיל.
תכונות סידוריות (Ordinal Attributes) - ניתן לצל לפיצול מרובה ולפיצול בינארי. לדוגמה ממוצע ציוני סטודנט בתואר יכול להיות: נמוך / בינוני / גבוהה / מצטיין.
תכונות רציפות (Continuous Attributes) - לדוגמה התכונה ציון בקורס X ניתן לפצל כפיצול בינארי מעל 90 / מתחת ל-90, או כפיצול מרובה {מעל 90} / {80 עד 90} / {70 עד 80} / {60 עד 70} / {מתחת ל-60}.


בעיות המתאימות לעצי החלטה

לא כל בעיה מתאימה לחיזוי באמצעות עצי החלטה, לכן נוודא כי המאפיינים של הבעיה שלנו אכן מתאימים לחיזוי זה.

נדרוש שכל תצפית תיוצג ע"י סט קבוע של משתנים [במקרה שלנו: מין (Gender), מקום לידה (Place of Birth), ציון פסיכומטרי (Test Grade)].
כל משתנה מנבא יקבל מספר קטן של ערכים אפשריים שונים [במקרה שלנו לא רצינו להשתמש בכל הערכים האפשריים של ארץ לידה (Place of Birth) מכיוון שמס' הערכים האפשריים הוא גבוהה, לכן צמצמנו את הערכים האפשריים, והפכנו את המשתנה למשתנה בינארי].
מניחים שפונ' המטרה מקבלת ערכים בדידים כאשר כל ערך בודד מתייחס לאיזשהי מחלקה [במקרה שלנו חילקנו את משתנה המטרה - ציון ממוצע באונ' (GPA) ל-3 מרווחים].
אנו מצפים לקבל מעץ ההחלטה סט חוקים נפרדים [לדוגמה if (Test < 600 Then GPA = Low ו if (Test >= 600) Then GPA = Medium or High].
הנחה כי נתוני האימון יכולים להכיל רעש, טעויות. ובנוסף, כי ערכים יכולים להיות חסרים (ישנם אלגוריתמים שיודעים לטפל בערכים חסרים, אבל בנתיים נניח כי כל הערכים ידועים).


2. אלגוריתם לבניית עצי החלטה - ID3

אלגוריתם ID3 הומצא ע"י רוס קווינלן בשנת 1986 והינו אלגוריתם חמדני, ז"א שאנו לא מחפשים מודל אופטימלי באופן כללי, אלא בונים את המודל באמצעות כלל אצבע לפיו אנו מסתכלים אך ורק על הצעד הבא (בלי הצעדים שאחריו) ובוחרים את האפשרות הטובה ביותר לגביו (ניתן להשוות את הרעיון לפתרון בעיית מבוך בה אנו מסתכלים אך ורק על הצעד הבא לפי מה שמקדם אותנו לכיוון היציאה, בלי להסתכל על כל המבוך באופן כללי ולראות כי אולי הצעד הבא מקדם אותנו יותר לכיוון היציאה אך באופן כללי הוא מקדם אותנו לפינה סגורה ממנה לא ניתן לצאת. בעוד הצעד שהיה מוציא אותנו מהמבוך נראה לנו באותו שלב כצעד שלוקח אותנו אחורה, או פחות מקדם אותנו).

העץ נבנה מלמעלה למטה (מהשורש לעלים), כאשר בכל שלב אנו מבצעים חלוקה של תצפיות האימון לקבוצות באופן רקורסיבי. תחילה יש לנו קבוצה אחת, ז"א כל תצפיות האימון שייכות לאותה קבוצה והינה שורש העץ. בנוסף, אנו מניחים שכל המשתנים הינם מסוג קטגורי (ז"א מספר הערכים שהמשתנה יכול לקבל הינו מוגבל, סופי), לכן אם ישנו משתנה רציף אנו נבצע לו מראש דיסקרטיזציה. אז כאמור נתחיל מקדקוד השורש ונבצע חלוקה רקורסיבית עפ"י משתנים הנבחרים ע"י האלגוריתם, את המשתנה המנבא בכל שלב נבחר באמצעות כלל קבוע או משתנה סטטיסטי כלשהו.

מכיוון שהחלוקה הרקורסיבית יכולה להימשך עד אינסוף, ישנם כמה תנאי עצירה. בראש ובראשונה נעצור אם כל התצפיות בענף מסויים שייכות לאותו סיווג מכיוון שאין טעם לחלק אותן לקבוצות קטנות יותר. נעצור גם כאשר לא נשארו לנו משתנים כדי לבצע פיצול נוסף (כלומר, השתמשנו כבר בכל המשתנים האפשריים ברמות גבוהות יותר), במקרה כזה נסווג את העלה עפ"י הצבעת הרוב, ז"א הערך השכיח ביותר באותה קבוצת תצפיות (זאת מכיוון שאנו נדרשים לקבל חיזוי מוחלט, ז"א החלטה, לא מצב של 50% אופציה א ו 50% אופציה ב). כאשר מספר התצפיות בעלה ירד מתחת לאיזשהו סף עליו החלטנו מראש (לדוג' כאשר ישנם פחות מ-10 תצפיות נעצור את הבנייה ונסווג את העלה עפ"י השיטה של הצבעת הרוב).

בשלב הראשון באלגו' אנו יוצרים את צומת השורש, בודקים האם לכל התצפיות יש אותו ערך מטרה, במידה וכן האלגו' מחזיר עץ בעל קודקוד אחד המסווג עפ"י הערך של כל התצפיות. במידה ואין לכל התצפיות אותו ערך מטרה מפצלים את הקודקוד. כדי לפצל אותו אנו בוחרים במשתנה שמסוגל לפצל את הקודקוד בצורה הטובה ביותר (נדבר כיצד לעשות זאת בהמשך, כרגע נניח שיש לנו מדד כזה, השתמשנו בו, וכך בחרנו במשתנה המפצל). ברגע שבחרנו במשתנה A אנו מוסיפים לעץ ענף עבור כל משתנה של A. אם בקודקוד אין לנו תצפיות עבור משתנה מסויים, אנו יוצרים קודקוד עלה עבור אותו משתנה (ואותו קודקוד עלה יסווג עפ"י השכיח ביותר - הצבעת הרוב), אחרת (אם יש לנו תצפיות עבור המשתנה) ניצור תת עץ עפ"י האלגוריתם שתיארנו (בצורה רקורסיבית).



Information Gain
רווח אינפורמטיבי (Information Gain) היא השיטה הפשוטה ביותר לבחירת משתנה פיצול בעץ. בשיטה זאת אנו בוחרים את המשתנה בעל Information Gain הגבוהה ביותר. את הרווח האינפורמטיבי של משתנה A נחשב כהפרש בין האנטרופיה של התצפיות לפני הפיצול [(Info(D] להאנטרופיה של התצפיות אחרי הפיצול [(InfoA(D], ז"א (Gain(A) = Info(D) - InfoA(D.

נניח ש Pi היא הסתברות של תצפית כלשהי בקובץ האימון ושייכת לסיווג Ci, אנו יכולים להעריך את אותה הסתברות ע"י חלוקה של כל התצפיות בעלות הסיווג Ci בכלל התצפיות בקובץ האימון. בעזרת ההגדרה הזו נחשב את האנטרופיה, ז"א (Info(D) = -ΣPi*log2(Pi (הנוסחה המוכרת מחישוב האנטרופיה בפוסט הראשון). לאחר מכן נפצל את המשתנה A ל v הערכים השונים שלו, ונחשב את האנטרופיה עפ"י שיקלול של מספר התצפיות השייכות לכל אחד מהענפים ונכפיל את אותו אחוז תצפיות באנטרופיה של הענף המתאים., ז"א (InfoA(D) = Σ|Dj|/|D| * Info(Dj {כאשר Dj הוא מספר התצפיות השייכות לענף הנוכחי).

Info(D)ואתו המשתנה (A) שיהיה בעל ה (Gain(A המקסימלי, הוא המשתנה בו נשתמש על מנת לפצל את העץ.



נחשב את הנוסחאות על הדוגמה שלנו לקבלת סטודנטים לאונ' למשתנה ציון פסיכומטרי (Test Grade):

יש תצפית Low אחת, ולכן ההסתברות שלו היא 0.2 (1 מתוך 5 תצפיות), מינוס log2 של 0.2 זה 2.322.
ל Medium יש 2 תצפיות ולכן ההסתברות שלו היא 0.4, מינוס log2 של 0.4 זה 1.322.
גם ל High יש 2 תצפיות ולכן ההסתברות שלו היא 0.4, מינוס log2 של 0.4 זה 1.322.
לכן שיקלול של 2.322 + 1.322 + 1.322 = 1.522 וזהו ה (Info(D.



כדי לחשב את (InfoA(D ניצור את הטבלה הבאה ונסתכל על העמודות שלה (0-600, 600-700, ו Over 700). יש תצפית 1 ל Low ב 0-600, ותצפית אחת ל Medium ב 0-600, InfoA(D)ואף תצפית ל High באותו הסיווג, ולכן המשקל של כל אחת יהיה 0.5. ה מינוס log2 של 0.5 זה 1. נחשב את האנטרופיה של 0-600 ע"י הנוסחה של האנטרופיה הרגילה, (Info(D, ע"י p כפול (log2(p- של Low ועוד p כפול (log2(p- של Medium ונקבל 1. ונבצע אותו הדבר לסיווג 600-700 (האנטרופיה תצא 1) ולסיווג Over 700 (האנטרופיה תצא 0).

אחרי שיש לנו את האנטרופיה של כל סיווג בנפרד, נבצע את האנטרופיה הכללית של החלוקה, ז"א (InfoA(D ע"י שיקלול האנטרופיה של כל סיווג במשקל היחסי שלו (ז"א ב 0-600 יש לנו 2 תצפיות מתוך 5 אז המשקל יהיה 0.4). והאנטרופיה הכללית תצא 0.8. כעת כדי לחשב את ה Gain נחסיר את (InfoA(D שחישבו עכשיו מ (Info(D שחישבו בפסקה למעלה, ונקבל שה Gain שווה ל 0.722. בטבלאות למטה נחשב את (InfoA(D גם למשתנים מין (Gender) ומקום לידה (Place of Birth).

Information Gain GenderInformation Gain Place of Birth

























Decistion Treeוכשנעשה 1.522 [שזה (Info(D] פחות 1.351 [שזה מה שיצא לנו ה (InfoA(D של 2 המשתנים] נקבל 0.171. ז"א שה Gain של המשתנה פסיכומטרי (Test Grade) יצא הגבוהה ביותר ולכן נשתמש במשתנה הזה כדי לפצל את העץ. לאחר שנרכיב עץ החלטה כשהשורש שלו הוא המשתנה Test Grade ויוצאים ממנו 3 ענפים של 600>, 600-700, Over 700 נבדוק את התצפיות ששייכות לכל ענף  ובמידה וישנו רק ערך אחד נסווג על פיו, במידה וישנם כמה ערכים נבצע Information Gain גם לתצפיות האלו (עם המשתנים שנשארו, שהם Gender ו Place of Birth) ונבדוק עם איזה משתנה כדאי לפצל אותם, אם נקבל תצפיות שלכולן יש אותו ערך נסווג על פיו, אם לא נעשה שוב Information Gain, אם אין לנו יותר משתנים נסווג את העלה על פי הערך של רוב התצפיות. בתמונה משמאל ניתן לראות את עץ ההחלטה כפי שהוא צריך להיראות בסוף.



3. התאמת יתר (Overfitting)

Overfittingיתכן מצב בו העץ שבנינו עפ"י אחד האלגוריתמים (לדוג' ID3 שדיברנו עליו כבר) מתאים מצוין לנתוני האימון אך לא מדויק מספיק על נתוני המבחן (נתונים חדשים). מצב זה יכול לקרות כאשר אנו בונים עץ גדול מידי עם יותר מידי צמתים, יכול להיות שחלק מהענפים יתאימו באופן מדויק לנתוני האימון אך הם לא מייצגים איזשהי תבנית שחוזרת על עצמה אלא תופעות מקריות או אפילו תופעות אנומליות המתרחשות בנתוני האימון.

ניזכר שבסעיף הראשון בדקנו את הדיוק הממוצע של העץ לפי המשתנה Test Grade ומצאנו כי הדיוק הממוצע שלו הוא 60%, ז"א לחילופין הוא טועה ב-40% מהמקרים, לכן שגיאת האימון שלו error train = 40% (ונשלים את הענפים של העץ עפ"י הרוב או באופן אקראי, ניתן לראות בתמונה משמאל את ההשלמה שלי לעץ). אם ניקח את העץ הסופי שבנינו בסוף הסעיף השני, נראה כי הוא מתאים במדויק לנתוני האימון, ז"א שהדיוק הממוצע שלו הוא 100%, ושגיאת האימון שלו (error train) היא 0%. כעת, אם ניקח תצפיות חדשות (לדוג' את התצפיות בתמונה משמאל), ונניח שהן מייצגות את כלל האוכלוסיה, נבדוק אותן אל מול העץ הראשון (שהתקבל מ Test Grade עם שגיאת אימון של 40%) ונראה כי השגיאה שלו נתוני המבחן היא 0%, ז"א שהוא מדייק בכולן. לעומת זאת אם נקבל החלטה עפ"י העץ השני שבנינו (זה עם שגיאת האימון של 0%) נראה כי השגיאה שלו על נתוני המבחן היא 33%.



וקיבלנו מקרה קלאסי של התאמת יתר. לכן כעת נשאלת השאלה כיצד אפשר למנוע אותה? בראש ובראשונה אנחנו יכולים להניח שישנו איזשהו גודל אופטימלי של עץ החלטה.

כשהעץ קטן מאוד, ככל שמוסיפים לו יותר קודקודים (וכתוצאה מכך גם ענפים) דיוק האימון של עולה וגם דיוק המבחן, אך בשלב מסויים בעוד דיוק האימון ממשיך לעלות (כי את הקודקודים אנו קובעים עפ"י נתוני האימון אז ככל שיהיו יותר תתי מקרים כך העץ יתאים לאותם הנתונים טוב יותר), דיוק המבחן לעומתו נשאר אותו הדבר, ולאחר מכן אף מתחיל לרדת. מכך אנו מסיקים את הטענה שמעבר למס' קודקודים אין טעם להמשיך לפצל את העץ.

קיימת 2 גישות שונות בבניית עצי החלטה המונעות מצב זה:

גיזום מוקדם (Prepruning) - בגישה זו אנו מפסיקים לבנות את העץ מספיק מוקדם, ז"א לא נפצל איזשהו קודקוד, במידה והפיצול מביאה לירידה במדד איכות מסויים שבסופו של דבר יביא אותנו לקבל עץ שלא מספיק מדויק על נתוני המבחן. נציין כי הקושי בגישה זו הוא לבחור בסף המתאים כדי לעצור את הבניה של העץ.
גיזום מאוחר (Postpruning) - גישה זו הוכיחה את עצמה יותר מהגישה הקודמת, ובה אנחנו קודם כל בונים את העץ המלא (עם מס' מקסימלי של קודקודים) ואז, באופן הדרגתי, מנסים לצמצם את הענפים בעץ. אנו משתמשים בקובץ נפרד המכיל נתוני מבחן כדי לבדוק את העץ ולדעת לבחור (בעזרתם) את העץ המדויק ביותר (ז"א מתי להפסיק לגזום אותו).
דוגמה ל Postpruning ניתן לראות בדוגמה למעלה, היינו יכולים לקחת את העץ המלא שהגענו אליו בסוף סעיף 2, לגזום את המשתנים Place of Birth ו Gender ולהגיע לעץ Tree 1 בתמונה משמאל למעלה.



קביעת הגודל האופטימלי של העץ החלטה

קיימות כמה שיטות / גישות לקביעת הגודל האופטימלי של עץ החלטה. אחת מגישות, הנפוצה מבינהם היא גישה עליה דיברנו כבר, והיא אומרת לחלק את התצפיות (באופן אקראי) ל-2 קבוצות, כאשר 2/3 מהתצפיות יהיו בקבוצת האימון ממנה אנו בונים את העץ, ו-1/3 יהיו בקבוצת המבחן איתה אנו נבחן את המודלים השונים של העצים, ובעצם עם קבוצה זו נקבע מהו העץ שיבחר (מתוך העצים האפשריים).

שיטה נוספת, שכבר דיברנו עליה בפוסט הקודם, נקראת Cross Validation. אפשר להשתמש בשיטה זו גם כאן (כדי לקבוע את הגודל האופטימלי של העץ). נזכיר כי בשיטה זו נבחר מס' (לדוג' 10) שיקבע את מספר המודלים שנבנה, כאשר נחלק באופן אקראי את התצפיות ל-10 קבוצות, וכל פעם 9/10 קבוצות יהיו בקבוצת האימון שתבנה את מודל העץ, והקבוצה שנשארה (1/10) תשמש כקבוצת המבחן. ונעשה זאת 10 פעמים, כך שכל הקבוצות ישמשו פעם אחת כקבוצת המבחן.

גישה נוספת מבוססת על תורת האינפורמציה ונקראת אורך תיאור מינמלי (MDL). בשיטה זו אנו מפסיקים לבנות את העץ כאשר מביאים למינימום את אורך הקידוד הכולל של העץ וגם של השגיאות שלו.

ולבסוף, הגישה האחרונה (עליה נרחיב יותר) אומרת כי אין לנו צורך בקבוצת מבחן ואנו משתמשים בכל התצפיות בקבוצת האימון. וניעזר במשתנה סטטיסטי כלשהו (לדוג' חי בריבוע) על מנת להעריך האם כדאי לנו לפצל קודקוד מסויים על מנת לשפר את הדיוק של העץ.



גישת חי בריבוע (Chi-Square) מבוססת על המבחן הסטטיסטי הידוע הנקרא באותו שם. להלן הסימונים של המבחן:

A - המשתנה עליו אנו מעוניינים לבצע את הפיצול.
v - מסמנים את גודל התחום של אותו משתנה (מספר הערכים האפשריים של המשתנה).
Ci - זאת תת קבוצה של תצפיות המתייחסות לערך i של משתנה A.
c - מספר הסיווגים שיש לנו בתצפיות.
ej - מספר התצפיות ששיכות לסיווג j בבסיס הנתנוים C.
oij - מספר התצפיות ששייכות לסיווג j בתוך תת קבוצה Ci (אותה תת קבוצה שמתייחסת לערך i של המשתנה המפוצל A).
α (אלפא) - רמת מובהקות במבחן חי בריבוע.
השערות המבחן הן: השערת ה-0  (ההשערה אותה אנו מנסים לבחון באמצעות מבחן חי בריבוע) היא: המשתנה A אינו רלוונטי לסיווג של התצפיות בבסיס הנתונים C (כלומר אם נפצל את התצפיות, עפ"י הערכים של A אנחנו לא נוכל לשפר את דיוק החיזוי). וההשערה האלטרנטיבית היא: שלמשתנה A כן יש השפעה על התפלגות הסיווג בבסיס הנתונים C. במבחן זה משתמשים בסטטיסטיקה על מנת להשוות בין 2 התפלגויות כל אוכלוסיה נתונה, אך במקרה של בניית עץ, נשאלת השאלה האם התפלגות של משתנה המטרה מושפעת כתוצאה מפיצול עפ"י ערכים של איזשהו משתנה A.

על מנת לחשב את הסטטיסטי של המבחן נחשב קודם את המספר החזוי של תצפיות השייכות לסיווג j בקבוצה Ci: ניקח את אחוז התצפיות השייכות לסיווג j בכלל בסיס הנתונים ונכפיל במספר התצפיות הנמצאות בקבוצה Ci. לאחר מכן נחשב את הסטטיסטי של המבחן ע"י לקיחת ההפרש שבין מס' התצפיות הצפוי (עפ"י השערת ה-0) למס' התצפיות בפועל, ומחלקים במס' התצפיות הצפוי. בתמונה משמאל ניתן לראות את הנוסחאות (הראשונה לחישוב המספר החזוי של התצפיות, והשנייה לחישוב הסטטיסטי) (ואת מס' דרגות החופש של הסטטיסטי מחשבים ע"י מס' הערכים של המשתנה A פחות 1, כפול מס' הסיווגים פחות 1).

כלומר, אם השערת ה-0 נכונה, אנו מצפים שהערך של הסטטיסטי שלנו יהיה נמוך יותר מהערף הקריטי של חי בריבוע, לעומת זאת אם הערך גבוהה יותר מהערך הקריטי אז נדחה את השערת ה-0, ונפצל את הקודקוד.



דוגמה לחי בריבוע

ניזכר שלפני הפיצול החלוקה הייתה: סטודנט אחד = Low, שתי סטודנטים = Medium, ו-2 סטודנטים = High. וכעת אנו רוצים לדעת האם כדאי לנו לפצל עפ"י המשתנה Test Grade (הציון הפסיכומטרי).

עבור ערכים מתחת ל-600 (ז"א Test < 600) יש לנו 2 ערכים, אחד עם ממוצע Medium והשני עם ממוצע Low. נחשב את מס' התצפיות הצפוי (מכל סוג) עפ"י השערת ה-0: כיוון שיש לנו 2 תצפיות עם ערכים "מתחת ל-600" נכפיל את האחוז ב-2, ז"א e'low = (1/5)*2 = 0.4, ו e'medium = (2/5)*2 = 0.8, ו e'high = (2/5)*2 = 0.8. ז"א שבהינתן השערת ה-0, אנו מצפים לקבל 0.4 תצפיות בסיווג low, ו-0.8 תצפיות בסיווג medium, ו-0.8 תצפיות בסיווג high.
נלך לענף השני, Test בין 600 ל-700, ונראה שיש לנו שם 2 ערכים, אחד עם ממוצע Medium והשני עם ממוצע High. נעשה את אותו החישוב ונקבל את אותם התוצאות כמו בענף הראשון: ז"א e'low = (1/5)*2 = 0.4, ו e'medium = (2/5)*2 = 0.8, ו e'high = (2/5)*2 = 0.8.
נלך לענף השלישי, Test > 700, ונראה שיש לנו שם ערך אחד עם ממוצע High. נעשה את החישוב: e'low = (1/5)*1 = 0.2, ו e'medium = (2/5)*1 = 0.4, ו e'high = (2/5)*1 = 0.4.
בעזרת כל החישובים האלו נחשב לבסוף את הסטטיסטי (שיהיה שווה ל-3.75) וכאשר החי בריבוע ברמת מובהקות של 0.05 ועבור 4 דרגות חופש הוא שווה ל x²0.05(4) = 9.49. הגענו ל-4 דרגות חופש מכיוון שלמשתנה Test ha 3 ערכים 3-1 שווה ל-2, וגם לתצפיות עצמן יש 3 סיווגים אפשריים (Low, Medium, ן High) ו 3-1 זה שוב 2, וכשנכפיל 2*2 נקבל 4.

מכיוון שקיבלנו סטטיסטי נמוך יותר מהערך הקריטי של חי בריבוע לא נדחה את השערת ה-0, והמסקנה היא שעפ"י מבחן זה לא כדאי לפצל את הקודקוד עפ"י המשתנה של הציון הפסיכומטרי.



4. הפקת חוקים מעץ החלטה וחלוקה למרווחים

Decistion Treeלהלן (בצד שמאל) העץ שבנינו בסוף סעיף 2. בעץ הזה אנו רואים 5 עלים, וכל עלה כזה ניתן לנסח כחוק חיזוי. למשל: If (Test < 600) and (Place of Birth = Israel) Then Grade = Low וכך הלאה לכל העלים. אין הרבה מה להרחיב על הפקת החוקים, כל צומת בעץ היא תנאי של ה If שצריך להתקיים (and) ולבסוף הצומת הראשונה ממנה יצאנו תיהיה שווה לערך של העלה.

דסקרטיזציה (חלוקה למרווחים) של משתנים רציפים תוך כדי בנייה של עץ החלטה. נזכיר, כי עד כה הנחנו שכל משתנה שיש לנו בעץ הוא משתנה קטגורי, משתנה בדיד. וראינו גם שאם היה לנו דוגמאות שאם היה לנו איזשהוא משתנה רציף היה אפשר לחלק אותו מראש למרווחים. הגישה הפשוטה ביותר לחלוקה למרווחים מכונה דיסקרטיזציה בלתי מודרכת (Unsupervised Discretiztion) ודיברנו עליה בפוסט הקודם. בקצרה, בגישה זו מחלקים את המשתנה הרציף למרווחים בעלי רוחב זהה או בעלי תדירות זהה (bins). שיטה זו היא סטטית, מאחר שהשיטה קובעת את אופן החלוקה לכל משתנה במודל הסיווג.

מנגד, כדי לשפר את יעילות הסיווג פותחו כמה שיטות לדיסקרטיזציה סטטית מודרכת (Supervised Discretiztion Static) המבוססת על ערכי משתנה המטרה בכל רשומת אימון. הדוגמה הפופלרית לאלגוריתם הממש שיטה זו היא אלגו' לדיסקרטיזציה בינארית המבוסס על מינמיזציה של אנתרופיית משתנה המטרה. הלאגוריתם מתחיל בפיצול של מרווח יחיד. ולאחר מכן, כל תת מרווח מפוצל רקורסיבית. וקריטריון העצירה הוא עקרון ה MDL (עליו דיברנו בפוסט הקודם).

Supervised Discretzation Staticכדי להחליט על הפיצוללמרווחים נחשב את הרווח האינפורמטיבי (ההפרש בין האנתרופיה של לפני ואחרי הפיצול), ונבחר את הנק' עם הרווח המקסימלי (כמובן שנעשה זאת לכל נק' פיצול עד שנגיע לאחד מהקריטריונים לעצירה). בצד שמאל ניתן לראות את הנוסחה לחישוב, ולמטה מקרא שלה.

S - הסט הכולל של התצפיות.
A - המשתנה הרציף שאנו מעוניינים לפצל.
T - נק' הפיצול (הגבול בין 2 המרווחים).
S1 - כל התצפיות הנמצאות מתחת לנק' הפיצול.
S2 - כל התצפיות הנמצאות מעל לנק' הפיצול.




לסיכום

ראינו שעצי החלטה היא אחת הטכניקות הפופלריות ביותר לסיווג של תצפיות, את האלגוריתם הבסיסי לבניית עצי החלטה, ניתן לבחור את המשתנה הטוב ביותר לפיצול באמצעות מדדים שונים (כאשר האלגו' ID3 בוחר עפ"י הרווח האינפורמטיבי, Information gain, כלומר אותו משתנה שמביא אותו למקסימום). כאשר העץ גדול מידי ומתאים מידי לנתוני האימון יכולה להתעורר בעיה של התאמת יתר (Overfitting) וראינו את שיטת גיזום מוקדם ע"י עצירת בניית העץ ואת גיזום מאוחר המשמשות למנוע את התופעה.