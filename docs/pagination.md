
# 📄 Pagination (React)

Pagination divides your table data into pages, improving performance and usability when handling large datasets.

## Enable Pagination

To add pagination:

- Use `getPaginationRowModel` plugin
- Track current page and page size state
- Control pagination actions (next, previous, go to page)

## Basic Example

```tsx
import React, { useState } from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getPaginationRowModel,
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
  // Add more data to test pagination...
];

export function PaginationTable() {
  const [pagination, setPagination] = useState({
    pageIndex: 0,
    pageSize: 2,
  });

  const table = useReactTable({
    data,
    columns,
    state: { pagination },
    onPaginationChange: setPagination,
    getCoreRowModel: getCoreRowModel(),
    getPaginationRowModel: getPaginationRowModel(),
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
            <tr key={row.id}>
              {row.getVisibleCells().map(cell => (
                <td key={cell.id}>{cell.renderCell()}</td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>

      <div>
        <button
          onClick={() => table.previousPage()}
          disabled={!table.getCanPreviousPage()}
        >
          Previous
        </button>
        <button
          onClick={() => table.nextPage()}
          disabled={!table.getCanNextPage()}
        >
          Next
        </button>
        <span>
          Page{' '}
          <strong>
            {table.getState().pagination.pageIndex + 1} of{' '}
            {table.getPageCount()}
          </strong>
        </span>
      </div>
    </>
  );
}
````

## Key Points

* Use `getPaginationRowModel` to enable pagination logic.
* Maintain pagination state with `pageIndex` and `pageSize`.
* Use `onPaginationChange` to update pagination state.
* Use helper functions `nextPage()`, `previousPage()`, and `getCanNextPage()` to control navigation.
* Display current page info with `getState().pagination.pageIndex` and `getPageCount()`.

## Summary

Pagination helps you show data in manageable chunks, improving UX and performance for big tables.

➡️ Next: [Row Selection](row-selection.md)