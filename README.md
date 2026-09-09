# Dream App

A dream journal built with Ruby on Rails. Record your dreams, explore AI-generated interpretations and illustrations, and ask follow-up questions in a chat connected to each dream.

## Features

- Record a dream with its date, mood, and description.
- Generate a title, summary, themes, and symbols using OpenAI through RubyLLM.
- Create a surreal illustration in a background job, with a live Turbo update when it is ready.
- Browse your journal and filter dreams by theme or symbol.
- Ask follow-up questions with conversation history saved for each dream.
- Sign up with email and password or sign in with Google.

## Tech stack

- Ruby **3.3.5** and Rails **8.1**
- PostgreSQL
- RubyLLM and OpenAI for interpretation, chat, and image generation
- Devise and OmniAuth Google OAuth2 for authentication
- Active Storage and Cloudinary for images
- Bootstrap 5, Sass, Turbo, Stimulus, and import maps
- Solid Queue, Solid Cache, and Solid Cable for production infrastructure
- Docker and Kamal deployment configuration

## Local setup

### Prerequisites

Install Ruby 3.3.5, Bundler, and PostgreSQL. Start PostgreSQL and ensure your local database role can create databases. Connection settings are in [config/database.yml](config/database.yml).

You will also need an OpenAI API key for AI features and Cloudinary credentials for image storage. Google OAuth credentials are needed only if you want to use Google sign-in.

### 1. Clone your fork

Replace `YOUR_GITHUB_USERNAME` with the account that owns your fork:

```bash
git clone https://github.com/YOUR_GITHUB_USERNAME/dream-app.git
cd dream-app
```

### 2. Configure environment variables

Create `.env` in the project root before running setup:

```dotenv
OPENAI_API_KEY=your_openai_api_key
CLOUDINARY_URL=cloudinary://your_api_key:your_api_secret@your_cloud_name

# Optional: enable Google sign-in
GOOGLE_OAUTH_CLIENT_ID=your_google_client_id
GOOGLE_OAUTH_CLIENT_SECRET=your_google_client_secret
```

The application loads `.env` in development and test through `dotenv-rails`. This file is ignored by Git; keep actual credentials out of commits.

Development and production currently use Cloudinary. To store images on disk during local development instead, change `config.active_storage.service` to `:local` in [config/environments/development.rb](config/environments/development.rb). The local storage service is already defined in [config/storage.yml](config/storage.yml).

### 3. Prepare the application

```bash
bin/setup --skip-server
```

This installs missing gems, prepares the database, and clears logs and temporary files. On a fresh database, preparation also runs the seed file. The seed file deletes existing dreams when run, so use it only with disposable data. The `--reset` setup option also resets the database.

### 4. Start the server

```bash
bin/dev
```

Open <http://localhost:3000>. `bin/dev` starts the Rails server; no separate JavaScript build process is configured.

## Using the app

1. Create an account or sign in.
2. Add a dream description, date, and mood.
3. Read the generated interpretation. The illustration is generated asynchronously and appears when ready.
4. Open the dream's chat to ask follow-up questions.
5. Return to your journal to browse dreams or filter by theme and symbol.

Interpretation and chat call OpenAI during the request. Illustration generation runs through Active Job; production uses Solid Queue.

## Development checks

Run the Rails tests:

```bash
bin/rails test
```

Run code style and security checks:

```bash
bin/rubocop
bin/brakeman
bin/bundler-audit
bin/importmap audit
```

Run the full local CI workflow:

```bash
bin/ci
```

The workflow in [config/ci.rb](config/ci.rb) includes setup, style checks, security checks, Rails tests, and reseeding the test database.

## Project structure

| Path | Purpose |
| --- | --- |
| `app/controllers/dreams_controller.rb` | Dream creation, interpretation, and journal filtering |
| `app/controllers/messages_controller.rb` | Follow-up chat and AI responses |
| `app/models/` | Users, dreams, and messages |
| `app/schemas/dream_interpretation_schema.rb` | Structured AI interpretation format |
| `app/jobs/image_generation_job.rb` | Image generation, attachment, and Turbo updates |
| `app/views/` | Rails templates and shared UI components |
| `app/assets/stylesheets/` | Application styles |
| `config/routes.rb` | Application routes |

## Deployment

The repository includes a [Dockerfile](Dockerfile) and [Kamal configuration](config/deploy.yml). The Kamal configuration still contains example infrastructure values, including a placeholder server address and local registry. Configure your server, registry, domain, and secrets before deploying.

Production requires `DATABASE_URL`, `RAILS_MASTER_KEY`, `OPENAI_API_KEY`, and `CLOUDINARY_URL`, plus Google OAuth credentials if enabled. Configure these in the deployment environment; production does not load the development `.env` file. Ensure the database is prepared and Solid Queue is running for image generation. The supplied Kamal configuration enables the queue supervisor inside Puma with `SOLID_QUEUE_IN_PUMA`.

## Credits

This project is a fork of [giftaSantosa/dream-app](https://github.com/giftaSantosa/dream-app).

The Rails application was originally generated with the [Le Wagon Rails templates](https://github.com/lewagon/rails-templates).
