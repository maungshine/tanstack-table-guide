
# ⬆️ Sorting (React)

Sorting lets users reorder table rows by column values. TanStack Table makes sorting easy and flexible.

## Enable Sorting

To enable sorting, you need to:

- Add the sorting plugin: `getSortedRowModel`
- Track sorting state
- Update sorting state on user actions

## Basic Example

```tsx
import React, { useState } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  ColumnDef,
} from '@tanstack/react-table';

type Person = {
  firstName: string;
  lastName: string;
  age: number;
};

const columns: ColumnDef<Person>[] = [
  { accessorKey: 'firstName', header: 'First Name' },
  { accessorKey: 'lastName', header: 'Last Name' },
  { accessorKey: 'age', header: 'Age' },
];

const data: Person[] = [
  { firstName: 'Alice', lastName: 'Smith', age: 25 },
  { firstName: 'Bob', lastName: 'Johnson', age: 30 },
  { firstName: 'Carol', lastName: 'Brown', age: 22 },
];

export function SortingTable() {
  const [sorting, setSorting] = useState([]);

  const table = useReactTable({
    data,
    columns,
    state: { sorting },
    onSortingChange: setSorting,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: getSortedRowModel(),
  });

  return (
    <table>
      <thead>
        {table.getHeaderGroups().map(headerGroup => (
          <tr key={headerGroup.id}>
            {headerGroup.headers.map(header => (
              <th key={header.id} onClick={header.column.getToggleSortingHandler()}>
                {header.isPlaceholder ? null : (
                  <>
                    {header.column.columnDef.header}
                    {{
                      asc: ' 🔼',
                      desc: ' 🔽',
                    }[header.column.getIsSorted()] ?? null}
                  </>
                )}
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

## Key Points

* Use `getSortedRowModel` plugin to enable sorting logic.
* Track sorting state in React with `useState`.
* Attach `onSortingChange` to update sorting state.
* Use `header.column.getToggleSortingHandler()` on headers to toggle sorting on click.
* You can show sorting direction icons using `header.column.getIsSorted()`.

## Summary

Sorting gives users an intuitive way to reorder table data. You control state and UI while TanStack Table handles the logic.

➡️ Next: [Filtering](filtering.md)

