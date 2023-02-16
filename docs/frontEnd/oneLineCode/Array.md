# Array

## 值转数组

```javascript
const castArray = (value) => (Array.isArray(value) ? value : [value]);
const castArray = <T,_>(value: T | T[]): T[] => (Array.isArray(value) ? value : [value]);
castArray(1); // [1]
castArray([1, 2, 3]); // [1, 2, 3]
```

## 空数组

```javascript
const isEmpty = (arr) => Array.isArray(arr) && !arr.length;
const isEmpty = <T,_>(arr: T[]): boolean => Array.isArray(arr) && !arr.length;
isEmpty([]); // true
isEmpty([1, 2, 3]); // false
```

## 比较两个数组相等并且不考虑顺序

```javascript
const isEqual = (a, b) => JSON.stringify([...new Set(a)].sort()) === JSON.stringify([...new Set(b)].sort());
const isEqual = <T,_>(a: T[], b: T[]): boolean => JSON.stringify([...(new Set(a))].sort()) === JSON.stringify([...(new Set(b))].sort());
isEqual([1, 2, 3], [1, 2, 3]); // true
isEqual([1, 2, 3], [1, 3, 2]); // true
isEqual([1, 2, 3], [1, '2', 3]); // false
```

## 克隆数组

```javascript
// `arr` is an array
const clone = (arr) => arr.slice(0);

// Or
const clone = (arr) => [...arr];

// Or
const clone = (arr) => Array.from(arr);

// Or
const clone = (arr) => arr.map((x) => x);

// Or
const clone = (arr) => JSON.parse(JSON.stringify(arr));

// Or
const clone = (arr) => arr.concat([]);

// Or
const clone = (arr) => structuredClone(arr);
// `arr` is an array
const clone = <T,_>(arr: T[]): T[] => arr.slice(0);

// Or
const clone = <T,_>(arr: T[]): T[] => [...arr];

// Or
const clone = <T,_>(arr: T[]): T[] => Array.from(arr);

// Or
const clone = <T,_>(arr: T[]): T[] => arr.map((x) => x);

// Or
const clone = <T,_>(arr: T[]): T[] => JSON.parse(JSON.stringify(arr));

// Or
const clone = <T,_>(arr: T[]): T[] => arr.concat([]);

// Or
const clone = <T,_>(arr: T[]): T[] => structuredClone(arr);
```

## 对象数组转为对象

```javascript
const toObject = (arr, key) => arr.reduce((a, b) => ({ ...a, [b[key]]: b }), {});

// Or
const toObject = (arr, key) => Object.fromEntries(arr.map((it) => [it[key], it]));
const toObject = <T extends Record<string, any>, K extends keyof T>(arr: T[], key: K): Record<string, T> => (
    arr.reduce((a, b) => ({ ...a, [b[key]]: b }), {})
);

const toObject = <T extends Record<string, any>, K extends keyof T>(arr: T[], key: K): Record<string, T> => (
    Object.fromEntries(arr.map((it) => [it[key], it]))
);
toObject(
    [
        { id: '1', name: 'Alpha', gender: 'Male' },
        { id: '2', name: 'Bravo', gender: 'Male' },
        { id: '3', name: 'Charlie', gender: 'Female' },
    ],
    'id'
);
/* 
{
    '1': { id: '1', name: 'Alpha', gender: 'Male' },
    '2': { id: '2', name: 'Bravo', gender: 'Male' },
    '3': { id: '3', name: 'Charlie', gender: 'Female' },
}
*/
```

## 字符串数组转数字数组

```javascript
const toNumbers = (arr) => arr.map(Number);

// Or
const toNumbers = (arr) => arr.map((x) => +x);
const toNumbers = (arr: string[]): number[] => arr.map(Number);

// Or
const toNumbers = (arr: string[]): number[] => arr.map((x) => +x);
toNumbers(['2', '3', '4']); // [2, 3, 4]
```

## 统计对象数组属性数量

```javascript
const countBy = (arr, prop) => arr.reduce((prev, curr) => ((prev[curr[prop]] = ++prev[curr[prop]] || 1), prev), {});
const countBy = <T extends Record<string, string>, K extends keyof T>(arr: T[], prop: K): Record<string, number> => (
    arr.reduce((prev, curr) => ((prev[curr[prop]] = ++prev[curr[prop]] || 1), prev), {} as Record<string, number>)
);
countBy(
    [
        { branch: 'audi', model: 'q8', year: '2019' },
        { branch: 'audi', model: 'rs7', year: '2020' },
        { branch: 'ford', model: 'mustang', year: '2019' },
        { branch: 'ford', model: 'explorer', year: '2020' },
        { branch: 'bmw', model: 'x7', year: '2020' },
    ],
    'branch'
);

// { 'audi': 2, 'ford': 2, 'bmw': 1 }
```

## 统计数组中某个值出现次数

```javascript
const countOccurrences = (arr, val) => arr.reduce((a, v) => (v === val ? a + 1 : a), 0);

// Or
const countOccurrences = (arr, val) => arr.filter((item) => item === val).length;
const countOccurrences = <T,_>(arr: T[], val: T): number => arr.reduce((a, v) => (v === val ? a + 1 : a), 0);

// Or
const countOccurrences = <T,_>(arr: T[], val: T): number => arr.filter((item) => item === val).length;
countOccurrences([2, 1, 3, 3, 2, 3], 2); // 2
countOccurrences(['a', 'b', 'a', 'c', 'a', 'b'], 'a'); // 3
```

## 比较两个数组

```javascript
// `a` and `b` are arrays
const isEqual = (a, b) => JSON.stringify(a) === JSON.stringify(b);

// Or
const isEqual = (a, b) => a.length === b.length && a.every((v, i) => v === b[i]);
const isEqual = <T,_>(a: T[], b: T[]): boolean => JSON.stringify(a) === JSON.stringify(b);

// Or
const isEqual = <T,_>(a: T[], b: T[]): boolean => a.length === b.length && a.every((v, i) => v === b[i]);
isEqual([1, 2, 3], [1, 2, 3]); // true
isEqual([1, 2, 3], [1, '2', 3]); // false
```