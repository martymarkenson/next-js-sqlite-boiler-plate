# Next.js Boilerplate with SQLite Database

A Next.js boilerplate featuring a local **SQLite database** with Prisma ORM. Perfect for rapid prototyping and small-to-medium applications that don't require a full database server.

## 🗄️ Database Features

This boilerplate uses **SQLite** as its database, which means:

- **Zero configuration** - No database server to install or configure
- **Portable** - The entire database is a single file (`prisma/database.db`)
- **Perfect for development** - Start coding immediately without Docker or external services
- **Type-safe** - Full TypeScript support via Prisma Client
- **Easy migrations** - Version-controlled schema changes

### Why SQLite?

Because I was annoyed that supabase kept shutting down my old projects after 30 days.

## 🚀 Getting Started

### Prerequisites

- Node.js 18+ 
- npm, yarn, pnpm, or bun

### Installation

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd next-js-boiler-plate
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Set up environment variables**
   
   Create a `.env` file in the root directory:
   ```bash
   cp env.example .env
   ```
   
   Add your environment variables:
   ```env
   # API Authentication
   API_SECRET_KEY=your-secret-key-here
   
   # Database (automatically configured for SQLite)
   DATABASE_URL="file:./database.db"
   ```

4. **Initialize the database**
   
   Run Prisma migrations to create your SQLite database:
   ```bash
   npm run db:migrate
   ```
   
   This will:
   - Create `prisma/database.db` (your SQLite database file)
   - Run all migrations to set up the schema
   - Generate the Prisma Client

5. **Start the development server**
   ```bash
   npm run dev
   ```
   
   Open [http://localhost:3000](http://localhost:3000) to see your app!

## 📦 Database Management

### Available Database Commands

```bash
# Run migrations (creates/updates database schema)
npm run db:migrate

# Push schema changes without creating migration files
npm run db:push

# Open Prisma Studio (visual database editor)
npm run db:studio
```

### Prisma Studio

The easiest way to view and edit your SQLite database:

```bash
npm run db:studio
```

This opens a visual interface at `http://localhost:5555` where you can:
- Browse all your data
- Create, update, and delete records
- Test queries
- Inspect relationships

### Database Schema

The current schema (defined in `prisma/schema.prisma`) includes:

```prisma
model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String
  published Boolean  @default(false)
  createdAt DateTime @default(now())
}
```

To modify the schema:
1. Edit `prisma/schema.prisma`
2. Run `npm run db:migrate` to apply changes
3. Prisma Client will automatically regenerate with new types

## 🛠️ Tech Stack

- **Framework**: [Next.js 15](https://nextjs.org/) (App Router)
- **Language**: [TypeScript](https://www.typescriptlang.org/)
- **Database**: [SQLite](https://www.sqlite.org/) with [Prisma ORM](https://www.prisma.io/)
- **Styling**: [Tailwind CSS 4](https://tailwindcss.com/)
- **Runtime**: [React 19](https://react.dev/)

## 📁 Project Structure

```
├── prisma/
│   ├── schema.prisma          # Database schema definition
│   ├── database.db            # SQLite database file (gitignored)
│   └── migrations/            # Migration history
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   └── posts/        # Example API routes with auth
│   │   ├── page.tsx          # Home page
│   │   └── layout.tsx        # Root layout
│   └── lib/
│       └── db.ts             # Prisma client instance
├── .env                      # Environment variables (gitignored)
└── package.json
```

## 🔌 API Routes

### Example: Posts API

The boilerplate includes authenticated CRUD endpoints:

**GET /api/posts** - Fetch all posts
```bash
curl -H "x-api-key: your-secret-key" http://localhost:3000/api/posts
```

**POST /api/posts** - Create a new post
```bash
curl -X POST \
  -H "x-api-key: your-secret-key" \
  -H "Content-Type: application/json" \
  -d '{"title":"Hello","content":"World"}' \
  http://localhost:3000/api/posts
```

## 🔒 Security Notes

- The `.env` file containing your `API_SECRET_KEY` is gitignored
- The SQLite database file is gitignored to prevent committing local data
- Change the `API_SECRET_KEY` in production
- For production, consider migrating to PostgreSQL/MySQL for better concurrency

## 📝 Scripts

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
npm run db:migrate   # Run database migrations
npm run db:push      # Push schema changes to database
npm run db:studio    # Open Prisma Studio
```

## 🚢 Deployment

### Database Considerations

For production deployment:

1. **Keep SQLite** (suitable for low-traffic apps):
   - Deploy the entire project including migrations
   - Ensure the database file has write permissions
   - Good for: Vercel, Netlify, Railway

2. **Migrate to PostgreSQL/MySQL** (recommended for high-traffic):
   - Update `prisma/schema.prisma` datasource
   - Run migrations on the new database
   - Better concurrency and performance


### Deploy to Coolify

[Coolify](https://coolify.io/) is a self-hosted alternative to Heroku, Netlify, and Vercel. It's perfect for deploying this boilerplate on your own infrastructure.

#### Prerequisites

- A Coolify instance running (self-hosted or managed)
- A Git repository (GitHub, GitLab, or Gitea)
- SSH access to your Coolify server (for database file persistence)

#### Deployment Steps

1. **Push your code to a Git repository**
   ```bash
   git remote add origin <your-repo-url>
   git push -u origin main
   ```

2. **Create a new project in Coolify**
   - Log into your Coolify dashboard
   - Click "Add a new resource" → "Public Repository" or connect your Git source
   - Select your repository and branch (usually `main`)

3. **Configure build settings**
   - **Build Pack**: Nixpacks (auto-detected)
   - **Port**: 3000 (Next.js default)
   - **Build Command**: `npm run build` (auto-detected)
   - **Start Command**: `npm start` (auto-detected)

4. **Set environment variables**
   
   In the Coolify environment variables section, add:
   ```env
   API_SECRET_KEY=your-production-secret-key
   DATABASE_URL=file:./prisma/database.db
   NODE_ENV=production
   ```

5. **Configure persistent storage for SQLite**
   
   **Important**: SQLite needs persistent storage to keep data between deployments.
   
   - Go to "Storage" tab in your Coolify application
   - Add a new volume:
     - **Source Path**: `/app/prisma` (or your server path)
     - **Destination Path**: `/app/prisma`
   - This ensures your `database.db` file persists across deployments

6. **Add database initialization script**
   
   To run migrations on first deployment, you can:
   
   **Option A**: Use the `nixpacks.toml` file (already included):
   ```toml
   [phases.setup]
   cmds = ['npm run db:migrate']
   ```
   
   **Option B**: SSH into your Coolify container and run:
   ```bash
   npm run db:migrate
   ```

7. **Deploy**
   - Click "Deploy" in Coolify
   - Monitor the build logs
   - Once deployed, Coolify will provide you with a URL

8. **Verify deployment**
   ```bash
   curl -H "x-api-key: your-secret-key" https://your-app.coolify.io/api/posts
   ```

#### Coolify-Specific Tips

- **Auto-deployments**: Enable webhook deployments for automatic updates on git push
- **Custom domains**: Add your domain in the Coolify domain settings
- **SSL certificates**: Coolify auto-provisions Let's Encrypt certificates
- **Health checks**: Coolify monitors your app and restarts if needed
- **Database backups**: Set up regular backups of the `/app/prisma` volume
- **Logs**: Access logs directly in the Coolify dashboard

#### Scaling Considerations

If your app grows beyond SQLite's capabilities on Coolify:
1. Deploy a PostgreSQL database in Coolify (separate resource)
2. Update your `DATABASE_URL` to point to the PostgreSQL instance
3. Run migrations with the new database URL
4. Remove the SQLite volume mount

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🔗 Learn More

- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
