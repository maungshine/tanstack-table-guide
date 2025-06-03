
# 📄 Row Selection (React)

Row selection lets users select one or multiple rows, useful for bulk actions or highlighting.

## Enable Row Selection

- Use `getRowSelectionRowModel` plugin
- Track selected rows state (`rowSelection`)
- Use checkboxes or custom UI to toggle selection

## Basic Example

```tsx
import React, { useState } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getRowSelectionRowModel,
  ColumnDef,
} from '@tanstack/react-table';

type Person = {
  firstName: string;
  lastName: string;
  age: number;
};

const columns: ColumnDef<Person>[] = [
  {
    id: 'select',
    header: ({ table }) => (
      <input
        type="checkbox"
        checked={table.getIsAllPageRowsSelected()}
        onChange={table.getToggleAllPageRowsSelectedHandler()}
        aria-label="Select all rows"
      />
    ),
    cell: ({ row }) => (
      <input
        type="checkbox"
        checked={row.getIsSelected()}
        disabled={!row.getCanSelect()}
        onChange={row.getToggleSelectedHandler()}
        aria-label={`Select row ${row.id}`}
      />
    ),
  },
  { accessorKey: 'firstName', header: 'First Name' },
  { accessorKey: 'lastName', header: 'Last Name' },
  { accessorKey: 'age', header: 'Age' },
];

const data: Person[] = [
  { firstName: 'Alice', lastName: 'Smith', age: 25 },
  { firstName: 'Bob', lastName: 'Johnson', age: 30 },
  { firstName: 'Carol', lastName: 'Brown', age: 22 },
];

export function RowSelectionTable() {
  const [rowSelection, setRowSelection] = useState({});

  const table = useReactTable({
    data,
    columns,
    state: { rowSelection },
    onRowSelectionChange: setRowSelection,
    getCoreRowModel: getCoreRowModel(),
    getRowSelectionRowModel: getRowSelectionRowModel(),
  });

  return (
    <>
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
            <tr key={row.id} className={row.getIsSelected() ? 'selected' : ''}>
              {row.getVisibleCells().map(cell => (
                <td key={cell.id}>{cell.renderCell()}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>

      <pre>
        <code>{JSON.stringify(rowSelection, null, 2)}</code>
      </pre>
    </>
  );
}
````

## Key Points

* Use `getRowSelectionRowModel` to enable selection logic.
* Track selected rows with `rowSelection` state.
* Use `onRowSelectionChange` to update the state.
* Provide UI (checkboxes or buttons) that call `getToggleSelectedHandler()` or `getToggleAllPageRowsSelectedHandler()`.
* You can access selected rows via `table.getSelectedRowModel().rows`.

## Summary

Row selection lets users interact with rows for further actions like deleting, exporting, or editing multiple rows at once.

➡️ Next: [Customizing Cells](custom-cells.md)

