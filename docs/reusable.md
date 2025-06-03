# 🔁 Creating a Reusable Table Component (React)

One of the best ways to use TanStack Table in real----

## 3. Enhanced Reusable Component with Common Features

Let's create a more feature-rich version that includes sorting, filtering, and pagination:

```tsx
// components/EnhancedDataTable.tsx
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  flexRender,
  ColumnDef,
  SortingState,
  ColumnFiltersState,
} from '@tanstack/react-table';
import React, { useState } from 'react';

type EnhancedDataTableProps<T> = {
  columns: ColumnDef<T, any>[];
  data: T[];
  enableSorting?: boolean;
  enableFiltering?: boolean;
  enablePagination?: boolean;
  className?: string;
};

export function EnhancedDataTable<T>({ 
  columns, 
  data,
  enableSorting = true,
  enableFiltering = true,
  enablePagination = true,
  className = '' 
}: EnhancedDataTableProps<T>) {
  const [sorting, setSorting] = useState<SortingState>([]);
  const [columnFilters, setColumnFilters] = useState<ColumnFiltersState>([]);
  const [globalFilter, setGlobalFilter] = useState('');

  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: enableSorting ? getSortedRowModel() : undefined,
    getFilteredRowModel: enableFiltering ? getFilteredRowModel() : undefined,
    getPaginationRowModel: enablePagination ? getPaginationRowModel() : undefined,
    onSortingChange: setSorting,
    onColumnFiltersChange: setColumnFilters,
    onGlobalFilterChange: setGlobalFilter,
    state: {
      sorting,
      columnFilters,
      globalFilter,
    },
  });

  return (
    <div className={`space-y-4 ${className}`}>
      {/* Global Search */}
      {enableFiltering && (
        <div className="flex items-center space-x-2">
          <input
            value={globalFilter ?? ''}
            onChange={(e) => setGlobalFilter(e.target.value)}
            className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            placeholder="Search all columns..."
          />
        </div>
      )}

      {/* Table */}
      <div className="overflow-x-auto">
        <table className="min-w-full border-collapse border border-gray-300">
          <thead className="bg-gray-50">
            {table.getHeaderGroups().map(headerGroup => (
              <tr key={headerGroup.id}>
                {headerGroup.headers.map(header => (
                  <th 
                    key={header.id}
                    className="border border-gray-300 px-4 py-2 text-left font-semibold"
                  >
                    {header.isPlaceholder ? null : (
                      <div
                        className={`flex items-center space-x-1 ${
                          header.column.getCanSort() ? 'cursor-pointer select-none' : ''
                        }`}
                        onClick={header.column.getToggleSortingHandler()}
                      >
                        <span>
                          {flexRender(
                            header.column.columnDef.header,
                            header.getContext()
                          )}
                        </span>
                        {enableSorting && header.column.getCanSort() && (
                          <span className="text-gray-400">
                            {{
                              asc: ' ↑',
                              desc: ' ↓',
                            }[header.column.getIsSorted() as string] ?? ' ↕'}
                          </span>
                        )}
                      </div>
                    )}
                  </th>
                ))}
              </tr>
            ))}
          </thead>
          <tbody>
            {table.getRowModel().rows.map(row => (
              <tr key={row.id} className="hover:bg-gray-50">
                {row.getVisibleCells().map(cell => (
                  <td 
                    key={cell.id}
                    className="border border-gray-300 px-4 py-2"
                  >
                    {flexRender(
                      cell.column.columnDef.cell,
                      cell.getContext()
                    )}
                  </td>
                ))}
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {/* Pagination */}
      {enablePagination && (
        <div className="flex items-center justify-between">
          <div className="flex items-center space-x-2">
            <button
              className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50"
              onClick={() => table.setPageIndex(0)}
              disabled={!table.getCanPreviousPage()}
            >
              {'<<'}
            </button>
            <button
              className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50"
              onClick={() => table.previousPage()}
              disabled={!table.getCanPreviousPage()}
            >
              {'<'}
            </button>
            <button
              className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50"
              onClick={() => table.nextPage()}
              disabled={!table.getCanNextPage()}
            >
              {'>'}
            </button>
            <button
              className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50"
              onClick={() => table.setPageIndex(table.getPageCount() - 1)}
              disabled={!table.getCanNextPage()}
            >
              {'>>'}
            </button>
          </div>
          <span className="flex items-center space-x-1">
            <div>Page</div>
            <strong>
              {table.getState().pagination.pageIndex + 1} of{' '}
              {table.getPageCount()}
            </strong>
          </span>
          <select
            value={table.getState().pagination.pageSize}
            onChange={e => table.setPageSize(Number(e.target.value))}
            className="px-2 py-1 border border-gray-300 rounded"
          >
            {[10, 20, 30, 40, 50].map(pageSize => (
              <option key={pageSize} value={pageSize}>
                Show {pageSize}
              </option>
            ))}
          </select>
        </div>
      )}
    </div>
  );
}
```

### Using the Enhanced Component

```tsx
// pages/EnhancedUsersPage.tsx
import { EnhancedDataTable } from '../components/EnhancedDataTable';
import { ColumnDef } from '@tanstack/react-table';

type User = {
  id: number;
  name: string;
  email: string;
  role: string;
  status: 'active' | 'inactive';
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
  {
    header: 'Role',
    accessorKey: 'role',
  },
  {
    header: 'Status',
    accessorKey: 'status',
    cell: ({ getValue }) => {
      const status = getValue<string>();
      return (
        <span className={`px-2 py-1 rounded text-sm ${
          status === 'active' 
            ? 'bg-green-100 text-green-800' 
            : 'bg-red-100 text-red-800'
        }`}>
          {status}
        </span>
      );
    },
  },
];

const userData: User[] = [
  { id: 1, name: 'Alice Johnson', email: 'alice@company.com', role: 'Admin', status: 'active' },
  { id: 2, name: 'Bob Smith', email: 'bob@company.com', role: 'User', status: 'active' },
  { id: 3, name: 'Carol White', email: 'carol@company.com', role: 'Editor', status: 'inactive' },
  // ... more users
];

export default function EnhancedUsersPage() {
  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">Enhanced Users Table</h1>
      <EnhancedDataTable 
        columns={columns} 
        data={userData}
        enableSorting={true}
        enableFiltering={true}
        enablePagination={true}
      />
    </div>
  );
}
```

---

## 4. Creating Column Helpers

To make column definitions even more reusable, create helper functions:

```tsx
// utils/columnHelpers.tsx
import { ColumnDef } from '@tanstack/react-table';

export const createTextColumn = <T,>(
  accessorKey: keyof T,
  header: string
): ColumnDef<T> => ({
  header,
  accessorKey: accessorKey as string,
});

export const createNumberColumn = <T,>(
  accessorKey: keyof T,
  header: string,
  formatter?: (value: number) => string
): ColumnDef<T> => ({
  header,
  accessorKey: accessorKey as string,
  cell: ({ getValue }) => {
    const value = getValue<number>();
    return formatter ? formatter(value) : value.toString();
  },
});

export const createStatusColumn = <T,>(
  accessorKey: keyof T,
  header: string,
  statusConfig: Record<string, { color: string; bgColor: string }>
): ColumnDef<T> => ({
  header,
  accessorKey: accessorKey as string,
  cell: ({ getValue }) => {
    const status = getValue<string>();
    const config = statusConfig[status] || { color: 'text-gray-800', bgColor: 'bg-gray-100' };
    return (
      <span className={`px-2 py-1 rounded text-sm ${config.bgColor} ${config.color}`}>
        {status}
      </span>
    );
  },
});

export const createDateColumn = <T,>(
  accessorKey: keyof T,
  header: string,
  dateFormat?: Intl.DateTimeFormatOptions
): ColumnDef<T> => ({
  header,
  accessorKey: accessorKey as string,
  cell: ({ getValue }) => {
    const date = getValue<Date>();
    return date.toLocaleDateString(undefined, dateFormat);
  },
});
```

### Using Column Helpers

```tsx
// pages/UsersWithHelpers.tsx
import { EnhancedDataTable } from '../components/EnhancedDataTable';
import { 
  createTextColumn, 
  createStatusColumn,
  createDateColumn 
} from '../utils/columnHelpers';

type User = {
  id: number;
  name: string;
  email: string;
  role: string;
  status: 'active' | 'inactive' | 'pending';
  createdAt: Date;
};

const columns = [
  createTextColumn<User>('id', 'ID'),
  createTextColumn<User>('name', 'Name'),
  createTextColumn<User>('email', 'Email'),
  createTextColumn<User>('role', 'Role'),
  createStatusColumn<User>('status', 'Status', {
    active: { color: 'text-green-800', bgColor: 'bg-green-100' },
    inactive: { color: 'text-red-800', bgColor: 'bg-red-100' },
    pending: { color: 'text-yellow-800', bgColor: 'bg-yellow-100' },
  }),
  createDateColumn<User>('createdAt', 'Created At', { 
    year: 'numeric', 
    month: 'short', 
    day: 'numeric' 
  }),
];

// Use with your data...
```

---

## 5. Best Practices for Reusable Tables

### 1. **Prop Flexibility**
Make your component flexible by accepting various configuration options:

```tsx
type DataTableProps<T> = {
  // Required props
  columns: ColumnDef<T, any>[];
  data: T[];
  
  // Optional feature toggles
  enableSorting?: boolean;
  enableFiltering?: boolean;
  enablePagination?: boolean;
  enableRowSelection?: boolean;
  
  // Styling options
  className?: string;
  tableClassName?: string;
  headerClassName?: string;
  rowClassName?: string;
  
  // Event handlers
  onRowClick?: (row: T) => void;
  onRowSelect?: (selectedRows: T[]) => void;
  
  // Pagination options
  initialPageSize?: number;
  pageSizeOptions?: number[];
};
```

### 2. **Theme Support**
Create theme variants for different contexts:

```tsx
const themes = {
  default: {
    table: 'min-w-full border-collapse border border-gray-300',
    header: 'bg-gray-50 border border-gray-300 px-4 py-2 text-left font-semibold',
    row: 'hover:bg-gray-50',
    cell: 'border border-gray-300 px-4 py-2',
  },
  minimal: {
    table: 'min-w-full',
    header: 'border-b border-gray-200 px-4 py-2 text-left font-semibold',
    row: 'border-b border-gray-100 hover:bg-gray-50',
    cell: 'px-4 py-2',
  },
  dark: {
    table: 'min-w-full border-collapse border border-gray-600',
    header: 'bg-gray-800 text-white border border-gray-600 px-4 py-2 text-left font-semibold',
    row: 'hover:bg-gray-700 text-white',
    cell: 'border border-gray-600 px-4 py-2',
  },
};
```

### 3. **Loading and Empty States**
Handle different data states gracefully:

```tsx
export function DataTable<T>({ columns, data, isLoading, emptyMessage }: Props) {
  // ... table setup

  if (isLoading) {
    return (
      <div className="flex justify-center py-8">
        <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-500"></div>
      </div>
    );
  }

  if (data.length === 0) {
    return (
      <div className="text-center py-8 text-gray-500">
        {emptyMessage || 'No data available'}
      </div>
    );
  }

  return (
    // ... table JSX
  );
}
```

### 4. **Accessibility**
Ensure your table is accessible:

- Use semantic HTML elements
- Add ARIA labels where needed
- Support keyboard navigation
- Ensure proper color contrast

```tsx
<table 
  className="min-w-full border-collapse border border-gray-300"
  role="table"
  aria-label="Data table"
>
  <thead className="bg-gray-50">
    {/* ... headers with proper scope attributes */}
  </thead>
  <tbody>
    {/* ... rows with proper row headers */}
  </tbody>
</table>
```

---

## ✅ Benefits of This Approach

- **Consistency**: All tables in your app look and behave the same way
- **Maintainability**: Update table logic in one place
- **Reusability**: Use the same component for different data types
- **Feature Rich**: Built-in sorting, filtering, and pagination
- **Type Safety**: Full TypeScript support with generics
- **Flexibility**: Easy to customize and extend
- **Performance**: Optimized rendering with TanStack Table

## 🚀 Next Steps

You can extend this component further by adding:

- **Row Selection**: Multi-select with checkboxes
- **Export/Import**: CSV, JSON, or Excel export functionality
- **Bulk Actions**: Select multiple rows and perform actions
- **Virtual Scrolling**: For handling large datasets
- **Column Resizing**: Allow users to adjust column widths
- **Column Reordering**: Drag and drop column reordering
- **Advanced Filtering**: Per-column filters and date range pickers
- **Server-Side Operations**: For handling large datasets with pagination, sorting, and filtering on the server

## 📚 Additional Examples

### Complete Production-Ready Component

Here's a more complete example that includes loading states, error handling, and additional features:

```tsx
// components/ProductionDataTable.tsx
import {
  useReactTable,
  getCoreRowModel,
  getSortedRowModel,
  getFilteredRowModel,
  getPaginationRowModel,
  flexRender,
  ColumnDef,
  SortingState,
  ColumnFiltersState,
} from '@tanstack/react-table';
import React, { useState } from 'react';

type ProductionDataTableProps<T> = {
  columns: ColumnDef<T, any>[];
  data: T[];
  isLoading?: boolean;
  error?: string;
  enableSorting?: boolean;
  enableFiltering?: boolean;
  enablePagination?: boolean;
  emptyMessage?: string;
  className?: string;
  onRowClick?: (row: T) => void;
};

export function ProductionDataTable<T>({ 
  columns, 
  data,
  isLoading = false,
  error,
  enableSorting = true,
  enableFiltering = true,
  enablePagination = true,
  emptyMessage = 'No data available',
  className = '',
  onRowClick
}: ProductionDataTableProps<T>) {
  const [sorting, setSorting] = useState<SortingState>([]);
  const [columnFilters, setColumnFilters] = useState<ColumnFiltersState>([]);
  const [globalFilter, setGlobalFilter] = useState('');

  const table = useReactTable({
    data,
    columns,
    getCoreRowModel: getCoreRowModel(),
    getSortedRowModel: enableSorting ? getSortedRowModel() : undefined,
    getFilteredRowModel: enableFiltering ? getFilteredRowModel() : undefined,
    getPaginationRowModel: enablePagination ? getPaginationRowModel() : undefined,
    onSortingChange: setSorting,
    onColumnFiltersChange: setColumnFilters,
    onGlobalFilterChange: setGlobalFilter,
    state: {
      sorting,
      columnFilters,
      globalFilter,
    },
  });

  // Error state
  if (error) {
    return (
      <div className="p-4 bg-red-50 border border-red-200 rounded-md">
        <p className="text-red-800">Error: {error}</p>
      </div>
    );
  }

  // Loading state
  if (isLoading) {
    return (
      <div className="flex justify-center items-center py-12">
        <div className="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-500"></div>
        <span className="ml-2 text-gray-600">Loading...</span>
      </div>
    );
  }

  // Empty state
  if (data.length === 0) {
    return (
      <div className="text-center py-12 text-gray-500">
        <p>{emptyMessage}</p>
      </div>
    );
  }

  return (
    <div className={`space-y-4 ${className}`}>
      {/* Global Search */}
      {enableFiltering && (
        <div className="flex items-center justify-between">
          <input
            value={globalFilter ?? ''}
            onChange={(e) => setGlobalFilter(e.target.value)}
            className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500 max-w-sm"
            placeholder="Search all columns..."
          />
          <div className="text-sm text-gray-500">
            Showing {table.getFilteredRowModel().rows.length} of {data.length} results
          </div>
        </div>
      )}

      {/* Table */}
      <div className="overflow-x-auto shadow ring-1 ring-black ring-opacity-5 md:rounded-lg">
        <table className="min-w-full divide-y divide-gray-300">
          <thead className="bg-gray-50">
            {table.getHeaderGroups().map(headerGroup => (
              <tr key={headerGroup.id}>
                {headerGroup.headers.map(header => (
                  <th 
                    key={header.id}
                    className="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider"
                  >
                    {header.isPlaceholder ? null : (
                      <div
                        className={`flex items-center space-x-1 ${
                          header.column.getCanSort() ? 'cursor-pointer select-none hover:text-gray-700' : ''
                        }`}
                        onClick={header.column.getToggleSortingHandler()}
                      >
                        <span>
                          {flexRender(
                            header.column.columnDef.header,
                            header.getContext()
                          )}
                        </span>
                        {enableSorting && header.column.getCanSort() && (
                          <span className="text-gray-400">
                            {{
                              asc: ' ↑',
                              desc: ' ↓',
                            }[header.column.getIsSorted() as string] ?? ' ↕'}
                          </span>
                        )}
                      </div>
                    )}
                  </th>
                ))}
              </tr>
            ))}
          </thead>
          <tbody className="bg-white divide-y divide-gray-200">
            {table.getRowModel().rows.map(row => (
              <tr 
                key={row.id} 
                className={`hover:bg-gray-50 ${onRowClick ? 'cursor-pointer' : ''}`}
                onClick={() => onRowClick?.(row.original)}
              >
                {row.getVisibleCells().map(cell => (
                  <td 
                    key={cell.id}
                    className="px-6 py-4 whitespace-nowrap text-sm text-gray-900"
                  >
                    {flexRender(
                      cell.column.columnDef.cell,
                      cell.getContext()
                    )}
                  </td>
                ))}
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {/* Pagination */}
      {enablePagination && (
        <div className="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6">
          <div className="flex-1 flex justify-between sm:hidden">
            <button
              onClick={() => table.previousPage()}
              disabled={!table.getCanPreviousPage()}
              className="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50"
            >
              Previous
            </button>
            <button
              onClick={() => table.nextPage()}
              disabled={!table.getCanNextPage()}
              className="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50 disabled:opacity-50"
            >
              Next
            </button>
          </div>
          <div className="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
            <div>
              <p className="text-sm text-gray-700">
                Showing{' '}
                <span className="font-medium">
                  {table.getState().pagination.pageIndex * table.getState().pagination.pageSize + 1}
                </span>{' '}
                to{' '}
                <span className="font-medium">
                  {Math.min(
                    (table.getState().pagination.pageIndex + 1) * table.getState().pagination.pageSize,
                    table.getFilteredRowModel().rows.length
                  )}
                </span>{' '}
                of{' '}
                <span className="font-medium">{table.getFilteredRowModel().rows.length}</span>{' '}
                results
              </p>
            </div>
            <div className="flex items-center space-x-2">
              <button
                onClick={() => table.setPageIndex(0)}
                disabled={!table.getCanPreviousPage()}
                className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50 hover:bg-gray-50"
              >
                {'<<'}
              </button>
              <button
                onClick={() => table.previousPage()}
                disabled={!table.getCanPreviousPage()}
                className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50 hover:bg-gray-50"
              >
                {'<'}
              </button>
              <span className="flex items-center space-x-1">
                <div>Page</div>
                <strong>
                  {table.getState().pagination.pageIndex + 1} of{' '}
                  {table.getPageCount()}
                </strong>
              </span>
              <button
                onClick={() => table.nextPage()}
                disabled={!table.getCanNextPage()}
                className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50 hover:bg-gray-50"
              >
                {'>'}
              </button>
              <button
                onClick={() => table.setPageIndex(table.getPageCount() - 1)}
                disabled={!table.getCanNextPage()}
                className="px-3 py-1 border border-gray-300 rounded disabled:opacity-50 hover:bg-gray-50"
              >
                {'>>'}
              </button>
              <select
                value={table.getState().pagination.pageSize}
                onChange={e => table.setPageSize(Number(e.target.value))}
                className="px-2 py-1 border border-gray-300 rounded"
              >
                {[10, 20, 30, 40, 50].map(pageSize => (
                  <option key={pageSize} value={pageSize}>
                    Show {pageSize}
                  </option>
                ))}
              </select>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}
```

This comprehensive reusable table component provides a solid foundation for most data table needs in your React application while maintaining flexibility and type safety.

## 💡 Tips for Implementation

1. **Start Simple**: Begin with the basic component and add features as needed
2. **Think About Performance**: For large datasets, consider virtualization or server-side pagination
3. **Customize Gradually**: Add styling and theming options based on your design system
4. **Test Thoroughly**: Test with different data types and edge cases
5. **Document Well**: Provide clear examples and prop documentation for your team

With this reusable table component, you'll have a powerful, flexible, and maintainable solution for displaying tabular data throughout your React application!