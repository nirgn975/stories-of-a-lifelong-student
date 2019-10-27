---
title: 'The Data Link Layer – שכבת הערוץ'
author: nirgn
layout: post
summary: "פוסט זה מתמקד בשכבה השניה במודל, לאחר שדיברנו על השכבה הראשונה, הפיזית (לרוב, השכבה הקשה ביותר להבנה לאנשי מדעי המחשב), אנו ממשיכים לשכבה השנייה (מלמטה), הלא היא שכבת הערוץ."
category: Computer Networks
---
פוסט זה מתמקד בשכבה השניה במודל, לאחר שדיברנו על השכבה הראשונה, הפיזית (לרוב, השכבה הקשה ביותר להבנה לאנשי מדעי המחשב), אנו ממשיכים לשכבה השנייה (מלמטה), הלא היא שכבת הערוץ.
בפוסט אבצע סקירה מלאה על השכבה ואפרט את הנושאים הבאים:

1. נושאים בתיכון שכבת הערוץ.
2. קודים לגילוי שגיאות ותיקונן.
3. פרוטוקולים בסיסיים לשכבת הערוץ.
4. פרוטוקולי חלון-הזזה.
5. דוגמאות לשכבת הערוץ ברשתות שונות.

<!--more-->

&nbsp;

### אז לפני שאנחנו מתחילים, מהי שכבת הערוץ?

שכבת הערוץ עוסקת, באופן כללי, באלגוריתמים להשגת תקשורת אמינה ויעילה של יחידות מידע שלמות הנקראות “מסגרות” (במקום ביטים בודדים כמו בשכבה הפיסית).

&nbsp;

### 1. נושאים בתיכון שכבת הערוץ

לשכבה זו יש כמה מטרות עקריות:
1. לספק ממשק לשירות שכבת הרשת.
2. להתמודד עם שגיאות שידור.
3. לווסת את זרימת הנתונים כך שמקלטים איטיים אינם יוצפו ע”י שולחים מהירים.

כדי להשיג את מטרות אלה, שכבת הערוץ לוקחת את הפאקטות (packets) שהיא מקבלת משכבת הרשת (השכבה שמעליה, השלישית במספר) ועוטפת אותם (מבצעת עליהם [אינקפסולציה – כימוס](http://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)) למסגרות (frames) לשידור. כל מסגרת מכילה Header (כותרת), Payload field (שדה להחזקת המנות), ו Trailer (גורר).

&nbsp;

**השירות הניתן לשכבת הרשת**
המטרה של שכבת הערוץ היא לספק שירותים לשכבת הרשת (השכבה שמעליה, עליה עדיין לא דיברנו). השירות העיקרי הוא העברת נתונים משכבת הרשת במכונת המקור אל שכבת הרשת במכונת היעד. במכונת המקור ישנה ישות כלשהי, נחשוב עליה כעל תהליך, הנמצא בשכבת הרשת שמספקת לשכבת הערוץ חתיכות מידע לשם העברתם למכונת היעד, כדי שיוכלו להימסר לשכבת הרשת שם. בפועל שכבת הערוץ מעבירה את חתיכות המידע האלו דרך השכבה הפיזית אל מכונת היעד, אך קל יותר לחשוב (וזו תיהיה הדרך בה אנו נשתמש מכאן והלאה) כעל שני תהליכי תהליכים בשכבת הערוץ המתקשרים בינהם ישירות (ז”א משכבת הערוץ במכונת המקור לשכבת הערוץ במכונת היעד, ללא השכבה הפיזית שבפועל מתווכת בינהם).

> דרך מחשבה זו מפשטת את מבנה ארכיטקטורת הרשת כולו, ומכיוון שבמאמר הקודם דיברנו על תפקיד השכבה הפיזית, כיצד היא פועלת, הבעיות והפתרונות שהממומשים בה, וכ’ו. אין לנו עוד צורך להתמקד בה, אנו יודעים שהיא תספק את השירות אותו היא אמורה לספק, וכל שאנו אמורים זה להעביר אליה את המידע שאנו רוצים לשדר בצורה עליה הסכמנו מראש (על כל אלה דיברנו במאמר הקודם, מי שלא קרא, מומלץ בחום לקרוא אותו לפני שאתם ממשיכים).

שכבת הערוץ נועדה לספק מגוון שירותים שונים, בפועל, השירותים יכולים להשתנות מפרוטוקול לפרוטוקול, שלוש אפשרויות סבירות שנשקול הן:

* שירות ללא חיבור שלא מאושר.
* שירות ללא חיבור מאושר.
* שירות מכוון קשר מאושר.

**שירות ללא חיבור שלא מאושר** – מורכב ממכונת מקור המשדרת מסגרות עצמאיות אל מכונת היעד מבלי שמכונת היעד מכירה בהן. אתרנט ([Ethernet](https://en.wikipedia.org/wiki/Ethernet)) הוא דוגמה טובה לפרוטורול בעל שכבת ערוץ המספקת סוג כזה של שירות. אם מסגרת הולכת לאיבוד בשל רעש על הקו, לא נעשה כל ניסיון לזהות את האובדן או להתאושש ממנו בשכבת הערץ. שירות כזה מתאים כאשר שיעור השגיאות הוא נמוך מאוד, ולכן ההתאוששות נשמרת לשכבות גבוהות יותר. כמו כן הוא מתאים גם לתעבורה בזמן אמת כגון שידור קול, שבו מידע שמאחר להגיע גרוע יותר ממידע שהגיע משובש (או לא הגיע בכלל), שכן בתקשורת בזמן אמת עדיף לנו לחוות ירידה של פריים או קטיעה קטנה של התקשורת (גליץ) מאשר להשהות אותה כדי לשלוח אישור שהמסגרת לא הגיע ולחכות שתישלח שוב.

**שירות ללא חיבור מאושר** – כל מסגרת הנשלחת מאושרת באופן נפרד. בדרך זו השולח יודע אם המסגרת הגיע בצורה נכונה אל יעדה ואף אם אבדה בדרך. אם המסגרת לא הגיע תוך פרק הזמן שסוכם מראש ניתן לשלוח אותה שוב. שירות כזה שימושי בערוצים לא אמינים כגון מערכות אלחוטיות, תקן 802.11 ([WiFi](https://en.wikipedia.org/wiki/IEEE_802.11)) הוא דוגמה טובה לערוץ כזה.

**שירות מכוון קשר מאושר** – בשירות זה מכונת המקור ומכונת היעד יוצרות חיבור עוד לפני תחילת העברת הנתונים. כל מסגרת הנשלחת דרך חיבור זה ממוספרת, ושכבת הערוץ מבטיחה כי כל מסגרת הנשלחת אכן מתקבלת, ואפילו יותר מכך, היא מבטיחה כי כל מסגרת התקבלה בדיוק פעם אחת, ושכל המסגרות התקבלו בדיוק בסדר הנכון. שירות שכזה מתאים בשידורים ארוכים ולא אמינים כמו ערוצי לווין או מעגלי טלפונים למרחקים ארוכים. כמובן שכמו “שירות ללא חיבור מאושר” אם מסגרת לא הגיעה אל יעדה או אבדה בדרך היא תישלח שוב, ואף אם הטיימר פקע ועדיין לא התקבל אישור שהמסגרת הגיע ליעדה, מכונת היעד תשלח את המסגרת פעם נוספת, מה שיגרום למס’ מסגרות זהות שיגיעו אל היעד (ומכונת היעד פשוט “תזרוק” אותן) דבר הגורם לבזבוז רוחב הפס. לשירות זה יש 3 שלבים: הראשון הוא הקמת החיבור ע”י כך ששני הצדדים מאתחלים את המשתנים והמונים הדרושים כדי לעקוב אחר המסגרות והאישורים. בשלב השני מסגרת אחת או יותר מעוברת למכונת היעד. ובשלב השלישי והאחרון הקשר משוחרר, והמשאבים (משתנים, חוצצים – Buffers, וכ’ו) משתחררים.

Unacknowledged connectionless service.

2. Acknowledged connectionless service.

3. Acknowledged connection-oriented service.

&nbsp;

**מסגור**

כעי