# temp

```javascript
function flattenObject(obj: any): any {
  const result: any = {};

  for (const key in obj) {
    if (Array.isArray(obj[key])) {
      obj[key].forEach((item, index) => {
        if (typeof item === 'object' && item !== null) {
          for (const subKey in item) {
            result[`${subKey}_${index}`] = item[subKey];
          }
        }
      });
    } else {
      result[key] = obj[key];
    }
  }

  return result;
}

// Example Usage:
const input = { prop1: 'a', prop2: 'b', prop3: [{ a: 1 }, { a: 2 }] };
const output = flattenObject(input);

console.log(output);
