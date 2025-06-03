
# ⚙️ Advanced Features (React)

TanStack Table offers many advanced features that let you build powerful, interactive tables. This section covers some common advanced capabilities you’ll want to use.

## Sorting

Add multi-column sorting with minimal setup.

```tsx
import React from 'react';
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  ColumnDef,
  SortingState,
} from '@tanstack/react-table';

type Person = { firstName: string; lastName: string; age: number };

export function SortingTable() {
  const [sorting, setSorting] = React.useState<SortingState>([]);

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
              <th
                key={header.id}
                onClick={header.column.getToggleSortingHandler()}
                style={{ cursor: 'pointer' }}
              >
                {header.isPlaceholder ? null : header.renderHeader()}
                {{
                  asc: ' 🔼',
                  desc: ' 🔽',
                }[header.column.getIsSorted() as string] ?? null}
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

## Filtering

TanStack Table supports powerful filtering capabilities using the `getFilteredRowModel` plugin and filter functions.

## Pagination

Use `getPaginationRowModel` to easily paginate rows client-side.

## Row Selection

Use `getRowSelection` and related APIs to enable selecting rows with checkboxes.

## Grouping

Group rows by one or more columns for better data organization.

## Expanding Rows

Expandable rows allow showing nested content or details.

## Controlled State

You can control sorting, filtering, pagination, and row selection state externally to integrate with your UI and server.

---

For more detailed examples on these features, check the official docs and examples.

➡️ Next: [Tips & Best Practices](tips.md)