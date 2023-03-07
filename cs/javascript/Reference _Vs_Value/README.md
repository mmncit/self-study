### Examples of pass by value:

- Primitives (`number`, `string`, `boolean`, `symbol`, `undefined`, and `null`)

```js
const a = 10;
const b = "Hi";
let c = a;
c = c + 1;
```

| Variable | Value  |
| -------- | ------ |
| `a`      | `10`   |
| `b`      | `"Hi"` |
| `c`      | `11`   |

### Examples of pass by reference:

- Objects
- Arrays
- Functions

```js
let d = [1, 2];
let e = d; // e and d has the same reference
d.push(3); // changing d also changes e
```

| Variable | Value    |
| -------- | -------- |
| `d`      | `<0x01>` |
| `e`      | `<0x01>` |

| Address  | Value       |
| -------- | ----------- |
| `<0x01>` | `[1, 2, 3]` |

```js
let d = [1, 2];
let e = [1, 2];
let f = d == e;
```

| Variable | Value    |
| -------- | -------- |
| `d`      | `<0x01>` |
| `e`      | `<0x02>` |
| `f`      | `false`  |

```js
let c = [1, 2];
function add(array, element) {
  array.push(element);
}
add(c, 3);
```

| Variable | Value    |
| -------- | -------- |
| `c`      | `<0x01>` |
| `array`  | `<0x01>` |

| Address  | Value       |
| -------- | ----------- |
| `<0x01>` | `[1, 2, 3]` |

```js
let c = [1, 2];
function add(array, element) {
  array = [...array, element];
}
add(c, 3);
```

| Variable | Value    |
| -------- | -------- |
| `c`      | `<0x01>` |
| `array`  | `<0x02>` |

| Address  | Value       |
| -------- | ----------- |
| `<0x01>` | `[1, 2]`    |
| `<0x02>` | `[1, 2, 3]` |
