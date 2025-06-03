
# 📄 Customizing Cells (React)

One of TanStack Table’s core strengths is its flexibility in rendering cells. You have full control over how each cell displays, allowing you to add custom formatting, buttons, icons, or interactive elements.

## Basic Cell Customization

By default, a cell renders the raw data value. You can override this by defining a `cell` property on your column definition.

```tsx
import React from 'react';
import {
  useReactTable,
  getCoreRowModel,
  ColumnDef,
} from '@tanstack/react-table';

type Person = {
  firstName: string;
  lastName: string;
  age: number;
};

const columns: ColumnDef<Person>[] = [
  {
    accessorKey: 'firstName',
    header: 'First Name',
    cell: info => <i>{info.getValue<string>()}</i>, // italicize first name
  },
  {
    accessorKey: 'lastName',
    header: 'Last Name',
  },
  {
    accessorKey: 'age',
    header: 'Age',
    cell: info => (
      <span style={{ color: info.getValue<number>() > 25 ? 'red' : 'green' }}>
        {info.getValue<number>()}
      </span>
    ),
  },
];

const data: Person[] = [
  { firstName: 'Alice', lastName: 'Smith', age: 25 },
  { firstName: 'Bob', lastName: 'Johnson', age: 30 },
  { firstName: 'Carol', lastName: 'Brown', age: 22 },
];

export function CustomCellsTable() {
  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
  });

  return (
    <table>
      <thead>
        {table.getHeaderGroups().map(headerGroup => (
          <tr key={headerGroup.id}>
            {headerGroup.headers.map(header => (
              <th key={header.id}>
                {header.isPlaceholder ? null : header.renderHeader()}
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody>
        {table.getRowModel().rows.map(row => (
          <tr key={row.id}>
            {row.getVisibleCells().map(cell => (
              <td key={cell.id}>{cell.renderCell()}</td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
````

## More Customization Tips

* Use `info.row` to access the entire row data inside your `cell` renderer.
* You can render buttons, links, icons, inputs, or any React component inside cells.
* Combine with conditional styling based on values.
* Use `getValue()` to get the raw cell value.
* Access other column values via `info.row.original` if needed.

## Summary

Customizing cells lets you create rich, interactive tables tailored to your UI needs.

➡️ Next: [Advanced Features](advanced.md)