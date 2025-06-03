
# 🧱 Basic Usage (React)

This section demonstrates how to set up a basic table using TanStack Table in a React application.

TanStack Table provides the logic — you render the UI. This means you can easily integrate it with any design system or style.

## ✨ Example: Basic Table

```tsx
import React from 'react';
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
} from '@tanstack/react-table';

const data = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
];

const columns = [
  {
    accessorKey: 'name',
    header: 'Name',
  },
  {
    accessorKey: 'age',
    header: 'Age',
  },
];

function MyTable() {
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
                {header.isPlaceholder ? null : flexRender(header.column.columnDef.header, header.getContext())}
              </th>
            ))}
          </tr>
        ))}
      </thead>
      <tbody>
        {table.getRowModel().rows.map(row => (
          <tr key={row.id}>
            {row.getVisibleCells().map(cell => (
              <td key={cell.id}>
                {flexRender(cell.column.columnDef.cell, cell.getContext())}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}

export default MyTable;
````

## 🔍 What’s Happening?

* `useReactTable`: Initializes the table logic with data and columns.
* `getCoreRowModel`: Provides the base row model (no sorting/filtering yet).
* `flexRender`: Renders header and cell content based on what you pass into the column definitions.
* Each `tr`, `th`, and `td` is rendered dynamically.

---

This is the foundation for more advanced features like sorting, filtering, pagination, and more.

➡️ Next: [Columns and Data](columns-and-data.md)

