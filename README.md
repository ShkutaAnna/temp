# temp

```javascript
getCurrentMonthDays(): { day: number, dayOfWeek: string }[] {
  const now = new Date();
  const year = now.getFullYear();
  const month = now.getMonth(); // 0-based index

  const daysInMonth = new Date(year, month + 1, 0).getDate();
  const daysArray = [];

  for (let day = 1; day <= daysInMonth; day++) {
    const date = new Date(year, month, day);
    daysArray.push({
      day,
      dayOfWeek: date.toLocaleDateString('en-US', { weekday: 'long' }) // Change locale if needed
    });
  }

  return daysArray;
}
