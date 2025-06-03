# 🔁 Creating a Reusable Table Component (React)

One of the best ways to use TanStack Table in real-world apps is by abstracting common logic into a **reusable table component**.

Let’s walk through how to build a basic reusable table component with TanStack Table in React.

---

## 1. Create `DataTable.tsx`

```tsx
// components/DataTable.tsx
import {
  useReactTable,
  getCoreRowModel,
  flexRender,
  ColumnDef,
} from '@tanstack/react-table';
import React from 'react';

type DataTableProps<T> = {
  columns: ColumnDef<T, any>[];
  data: T[];
};

export function DataTable<T>({ columns, data }: DataTableProps<T>) {
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
                {flexRender(header.column.columnDef.header, header.getContext())}
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
```

---

## 2. Use the Component Anywhere

```tsx
// pages/Users.tsx
import { DataTable } from './components/DataTable';
import { ColumnDef } from '@tanstack/react-table';

type User = {
  id: number;
  name: string;
  email: string;
};

const columns: ColumnDef<User>[] = [
  {
    header: 'ID',
    accessorKey: 'id',
  },
  {
    header: 'Name',
    accessorKey: 'name',
  },
  {
    header: 'Email',
    accessorKey: 'email',
  },
];

const data: User[] = [
  { id: 1, name: 'Alice', email: 'alice@email.com' },
  { id: 2, name: 'Bob', email: 'bob@email.com' },
];

export default function UsersPage() {
  return <DataTable columns={columns} data={data} />;
}
```

---

## ✅ Benefits

- Abstracts common logic for all tables
- Easy to plug into different pages
- Keeps your UI consistent
- Encourages DRY (Don’t Repeat Yourself)

You can extend this component to support:

- Pagination
- Sorting
- Filtering
- Row selection
- Styling libraries (e.g., Tailwind, shadcn, Material UI)