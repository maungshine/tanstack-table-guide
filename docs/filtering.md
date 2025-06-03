
# 🔍 Filtering (React)

Filtering allows users to narrow down table rows by searching or applying criteria to columns.

## Enable Filtering

To enable filtering, you need to:

- Add the filtering plugin: `getFilteredRowModel`
- Track filter state
- Update filter state on user input

## Basic Example

```tsx
import React, { useState } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getFilteredRowModel,
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

export function FilteringTable() {
  const [columnFilters, setColumnFilters] = useState([]);

  const table = useReactTable({
    data,
    columns,
    state: { columnFilters },
    onColumnFiltersChange: setColumnFilters,
    getCoreRowModel: getCoreRowModel(),
    getFilteredRowModel: getFilteredRowModel(),
  });

  return (
    <>
      <input
        placeholder="Filter by first name"
        value={
          table
            .getColumn('firstName')
            ?.getFilterValue() ?? ''
        }
        onChange={e =>
          table.getColumn('firstName')?.setFilterValue(e.target.value)
        }
      />
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
    </>
  );
}
````

## Key Points

* Use `getFilteredRowModel` plugin to enable filtering logic.
* Maintain filter state with React's `useState`.
* Use `onColumnFiltersChange` to update filter state.
* Access and set filters on columns with `getColumn` and `setFilterValue`.
* You can create inputs for each column or a global filter.

## Summary

Filtering helps users find specific data quickly by applying filter conditions on columns.

➡️ Next: [Pagination](pagination.md)
