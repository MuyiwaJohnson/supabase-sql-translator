# SupaQuery

A VS Code extension that translates Supabase JavaScript queries to SQL, HTTP requests, and cURL commands in real-time.

## 🚀 Features

- **Context Menu**: Right-click on selected Supabase query to translate
- **Keyboard Shortcut**: Use `Ctrl+Shift+T` to translate selected queries
- **Real-time Updates**: See translations as you type when the webview panel is open
- **Copy to Clipboard**: One-click copying for all translation formats

### Supported Operations

| JavaScript | SQL | HTTP | cURL |
|------------|-----|------|------|
| `.select()` | `SELECT` | `GET` | `curl -X GET` |
| `.insert()` | `INSERT` | `POST` | `curl -X POST` |
| `.update()` | `UPDATE` | `PATCH` | `curl -X PATCH` |
| `.delete()` | `DELETE` | `DELETE` | `curl -X DELETE` |
| `.upsert()` | `INSERT ON CONFLICT` | `POST` | `curl -X POST` |

### Advanced Features
- **Filtering**: `.eq()`, `.gt()`, `.like()`, `.in()`, `.contains()`, `.or()`, `.not()`
- **Joins**: Relationship queries with foreign keys
- **Auth**: `auth.uid()` patterns and RLS support
- **JSONB**: Full PostgreSQL JSONB operations

## Quick Examples

### Basic Queries
```javascript
// SELECT
supabase.from('users').select('id, name').eq('status', 'active')
// → SELECT id, name FROM users WHERE status = 'active'

// INSERT
supabase.from('users').insert({name: 'John', email: 'john@example.com'})
// → INSERT INTO users (name, email) VALUES ('John', 'john@example.com')

// UPDATE
supabase.from('users').eq('id', 1).update({name: 'Jane'})
// → UPDATE users SET name = 'Jane' WHERE id = 1
```

### Complex Queries
```javascript
// Relationships
supabase.from('users')
  .select('*, posts(title, content)')
  .eq('posts.published', true)
// → SELECT *, posts.title, posts.content FROM users JOIN posts ON posts.user_id = users.id WHERE posts.published = true

// JSONB
supabase.from('products')
  .select('metadata->>color')
  .contains('metadata', {size: 'XL'})
// → SELECT metadata->>color FROM products WHERE metadata @> '{"size":"XL"}'
```

## Installation

1. Clone this repository
2. Install dependencies: `npm install`
3. Compile: `npm run compile`
4. Press `F5` in VS Code to run in development mode

## Usage

1. **Context Menu**: Select a Supabase query and right-click
2. **Keyboard Shortcut**: Select a query and press `Ctrl+Shift+T`
3. **Real-time**: Keep the webview panel open for automatic updates

## ⚠️ Limitations

### Core Limitations
- **Non-SELECT Round-trip**: INSERT/UPDATE/DELETE cannot be translated back to Supabase JS
- **Dynamic Queries**: Cannot parse queries built at runtime
- **Ternary Operators**: Not supported in `.select()` statements
- **Schema Validation**: Cannot verify against actual database schema

### HTTP/cURL Limitations
- **Authentication**: No auth headers included
- **RLS Policies**: Cannot account for Row Level Security
- **Base URL**: Uses localhost; manual adjustment may be needed

### Performance
- **Large Queries**: May cause parsing delays
- **Real-time Updates**: Continuous parsing may impact performance

## Architecture

```
src/
├── parser/              # AST Processing with ts-morph
├── sql-generator/       # SQL string assembly
├── enhanced-translator.ts # Translation pipeline
├── http-translator.ts   # HTTP/cURL generation
├── webview-provider.ts  # VS Code webview
└── extension.ts         # Main extension
```

## Testing

```bash
npm run test:unit
```

**97 test cases** covering CRUD operations, filtering, joins, auth, JSONB, and edge cases.

## Dependencies

- **ts-morph**: TypeScript AST parsing
- **@supabase/sql-to-rest**: HTTP translation (SELECT only)
- **@types/vscode**: VS Code extension types

## Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request

## Roadmap

- [x] Full CRUD support
- [x] Real-time translation
- [x] Beautiful webview interface
- [ ] Schema introspection
- [ ] Authentication headers in HTTP/cURL
- [ ] SQL formatting options

## License

MIT License

---

**Built with ❤️ for the Supabase community** 