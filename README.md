# מחשבון הכנסות מפרסום דיגיטלי

מערכת לחישוב הכנסות והסתברויות חשיפה עבור מערכת פרסום במסכים דיגיטליים בבניינים.

## תיאור המערכת

המערכת מורכבת משני חלקים עיקריים:
1. מחשבון הכנסות מפרסום מתקדם - חישוב הכנסות והסתברויות חשיפה
2. השוואת מחירי פרסום - השוואה בין אמצעי פרסום שונים

### מחשבון הכנסות מפרסום מתקדם

המחשבון מאפשר:
- חישוב הכנסות צפויות מפרסום במסכים דיגיטליים
- חישוב הסתברויות חשיפה למודעות
- המלצה על מספר מודעות אופטימלי
- ניתוח גרפי של הכנסות והסתברויות

#### שיטות חישוב הסתברויות

המערכת משתמשת בשתי שיטות חישוב שונות להסתברויות חשיפה:

1. **הסתברות לראות את כל המודעות** (Probability to See All Ads):    ```javascript
    function calculateAllAdsProbability(movements, adCount, baseProb, days) {
        const singlePassProbability = baseProb / adCount;
        const dailyAttempts = movements * days;
        const missProb = Math.pow(1 - singlePassProbability, dailyAttempts);
        const seeAllProb = Math.pow(1 - missProb, adCount);
        return Math.min(100, seeAllProb * 100);
    }    ```
    שיטה זו מחשבת את ההסתברות לראות את כל המודעות בתקופה נתונה על ידי:
    - חישוב ההסתברות לפספס מודעה ספציפית בכל הניסיונות
    - חישוב ההסתברות לראות את כל המודעות (לא לפספס אף אחת)

2. **נוסחת אספן הקלפים** (Coupon Collector's Formula):    ```javascript
    function calculateOptimalAdsCount(movements, days) {
        const attempts = movements * days;
        // n = attempts/(ln(n) + γ)
        // γ = קבוע אוילר (0.5772156649)
        return Math.floor(attempts / (Math.log(n) + 0.5772156649));
    }    ```
    שיטה זו מחשבת את מספר המודעות האופטימלי כך שנצפה לראות את כולן בתקופה נתונה.

#### פונקציות החישוב

המערכת משתמשת בשתי פונקציות מרכזיות המבוססות על נוסחת אספן הקלפים:

1. **חישוב מספר מודעות אופטימלי** (`calculateOptimalAdsCount`):
```javascript
function calculateOptimalAdsCount(movements, days = 30) {
    const attempts = movements * days;
    const baseProb = +document.getElementById('adExposureProbability').value / 100;
    const EULER_GAMMA = 0.5772156649;
    
    // פותרת את המשוואה: n = (attempts * baseProb)/(ln(n) + γ)
    let n = 1;
    let prevN = 0;
    while (Math.abs(n - prevN) > 0.0001) {
        prevN = n;
        n = (attempts * baseProb) / (Math.log(prevN) + EULER_GAMMA);
    }
    return Math.floor(n);
}
```
- מטרה: מחשבת את **מספר המודעות האופטימלי** שכדאי להציג
- תוצאה: מספר שלם המייצג את מספר המודעות המומלץ
- שימוש: משמשת להמלצה על מספר המודעות האידיאלי

2. **חישוב הסתברות לצפייה בכל המודעות** (`calculateCollectorProbability`):
```javascript
function calculateCollectorProbability(movements, adCount, days = 30) {
    const attempts = movements * days;
    const EULER_GAMMA = 0.5772156649;
    const baseProb = +document.getElementById('adExposureProbability').value / 100;
    
    // מספר הניסיונות האופטימלי הדרוש
    const optimalAttempts = adCount * (Math.log(adCount) + EULER_GAMMA);
    
    // ההסתברות היא היחס בין מספר הניסיונות בפועל למספר האופטימלי
    return Math.min(100, (attempts * baseProb / optimalAttempts) * 100);
}
```
- מטרה: מחשבת את **ההסתברות לראות את כל המודעות** עבור מספר מודעות נתון
- תוצאה: אחוז (0-100) המייצג את ההסתברות לראות את כל המודעות
- שימוש: משמשת להצגת ההסתברויות היומיות/שבועיות/חודשיות

שתי הפונקציות מבוססות על אותה תיאוריה (בעיית אספן הקלפים) אבל משמשות למטרות שונות:
- `calculateOptimalAdsCount` עונה על השאלה "כמה מודעות כדאי להציג?"
- `calculateCollectorProbability` עונה על השאלה "מה הסיכוי לראות את כל המודעות שבחרנו להציג?"

#### ההבדל בין השיטות

1. **הסתברות לראות את כל המודעות**:
    - נותנת הסתברות מדויקת לראות את כל המודעות
    - מתחשבת בכל הפרמטרים (תנועות, הסתברות בסיסית)
    - מאפשרת לחשב הסתברויות לתקופות שונות
    - מתאימה יותר למודל העסקי של פרסום

2. **נוסחת אספן הקלפים**:
    - נותנת מספר מודעות אופטימלי
    - מבוססת על תיאוריה מתמטית של איסוף פריטים
    - פחות גמישה לפרמטרים נוספים
    - משמשת כהמלצה משלימה

## התקנה והפעלה

1. העתק את הקבצים למחשב
2. פתח את `index.html` בדפדפן
3. המערכת תטען עם ערכי ברירת מחדל

## טכנולוגיות

- HTML5
- Tailwind CSS
- Chart.js
- JavaScript טהור (Vanilla)
- python -m http.server

## מבנה הקבצים

- `index.html` - דף ראשי עם ניווט
- `main.html` - מחשבון הכנסות מפרסום מתקדם
- `ad_price_calculator.html` - השוואת מחירי פרסום

## רישיון

MIT License