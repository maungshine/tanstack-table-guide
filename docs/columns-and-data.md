
# 🗂 Columns and Data (React)

Understanding how to define columns and provide data is essential to using TanStack Table effectively.

## Defining Columns

Columns are defined as an array of objects, where each object describes a column’s behavior and rendering.

### Basic Column Definition

```tsx
const columns = [
  {
    accessorKey: 'firstName',  // Key from your data objects
    header: 'First Name',      // Column header label
  },
  {
    accessorKey: 'lastName',
    header: 'Last Name',
  },
  {
    accessorKey: 'age',
    header: 'Age',
  },
];
````

* `accessorKey`: Key to access data property.
* `header`: The text or React component shown in the header.

## Custom Cell Rendering

You can customize how cells render using the `cell` property:

```tsx
const columns = [
  {
    accessorKey: 'age',
    header: 'Age',
    cell: info => info.getValue() + ' years',
  },
];
```

This appends “years” after the age value.

## Supplying Data

Data is a simple array of objects matching the keys used in your columns.

```tsx
const data = [
  { firstName: 'Alice', lastName: 'Smith', age: 25 },
  { firstName: 'Bob', lastName: 'Johnson', age: 30 },
];
```

## Using with `useReactTable`

Pass your columns and data into `useReactTable`:

```tsx
const table = useReactTable({
  data,
  columns,
  getCoreRowModel: getCoreRowModel(),
});
```

## Summary

* Define columns with `accessorKey` and `header`.
* Customize cells using `cell` renderers.
* Provide data as an array of objects matching your column keys.

➡️ Next: [Sorting](sorting.md)