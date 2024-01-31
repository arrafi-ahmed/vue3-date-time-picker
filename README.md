# DateAndTimePicker Component

## Props

- **data** (optional): Accepts a data object as input from the parent component.
   ```javascript
   data = {
     startDate: '2024-01-16',
     startTime: '22:40',
     endDate: '2024-01-27',
     endTime: '22:40',
   }
   
- **type** (mandatory): Type of datetime picker.
    - `'datetime'`: Date and time picker.
    - `'date'`: Date-only picker.
    - `'time'`: Time-only picker.
- **format** (optional): Format for time in 12h or 24h format.
    - `'12'`: 12-hour format.
    - `'24'`: 24-hour format.
- **color** (optional): For changing the color of component parts.
    - `color = '#000000'`
- **step** (mandatory): Minute step. When the user selects a time, the nearest step minute is automatically selected.
    - `step = 5 (in minutes)`

## Actions

- **handleEmitDate** (mandatory): Emits user input data in the specified JSON format.
  ```javascript
  {
  startDate: '2024-01-16',
  startTime: '22:40',
  endDate: '2024-01-27',
  endTime: '22:40',
  }

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
