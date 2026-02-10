# Password Reset Page Setup

## Overview

The password reset page is now hosted on GitHub Pages at `reset-password.html`. It uses Supabase's client-side authentication API to handle password resets without requiring a backend server.

## Configuration

### Option 1: Using GitHub Secrets (Recommended)

This keeps credentials out of version control while still deploying them to the site.

1. **Add GitHub Secrets:**
   - Go to your repository → **Settings** → **Secrets and variables** → **Actions**
   - Click **New repository secret**
   - Add `SUPABASE_URL` with your Supabase project URL
   - Add `SUPABASE_ANON_KEY` with your Supabase anon key
   - Find these in Supabase Dashboard → **Settings** → **API**

2. **The GitHub Actions workflow will automatically inject these** when deploying

3. **No need to edit `reset-password.html`** - it will use placeholders that get replaced during build

### Option 2: Manual Configuration

If you prefer to set values directly in the file:

Edit `reset-password.html` and replace the placeholder values:

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL_HERE'; // e.g., 'https://your-project-id.supabase.co'
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY_HERE'; // The 'anon' or 'public' key
```

**Where to find these:**
1. Go to https://app.supabase.com
2. Select your project
3. Go to **Settings** → **API**
4. Copy:
   - **Project URL** → `SUPABASE_URL`
   - **anon public** key → `SUPABASE_ANON_KEY`

**Note:** The anon key is safe to expose in client-side code - it's designed for this purpose.

### Step 2: Update Supabase Redirect URLs

1. Go to Supabase Dashboard → **Authentication** → **URL Configuration**
2. Under **Redirect URLs**, add:
   ```
   https://www.envisiontherapeutics.com/reset-password
   https://envisiontherapeutics.github.io/reset-password
   ```
3. Set **Site URL** to: `https://www.envisiontherapeutics.com` (or your preferred domain)
4. Click **Save**

### Step 3: Deploy

**If using GitHub Secrets (Option 1):**
1. Commit and push changes to the repository
2. GitHub Actions will automatically build and deploy (check Actions tab)
3. The workflow will inject secrets into `reset-password.html` during build
4. Test at: `https://www.envisiontherapeutics.com/reset-password`

**If using manual configuration (Option 2):**
1. Edit `reset-password.html` with your credentials
2. Commit and push changes to the repository
3. GitHub Pages will automatically deploy (usually within 1-2 minutes)
4. Test at: `https://www.envisiontherapeutics.com/reset-password`

## How It Works

1. User requests password reset from mobile app
2. Supabase sends email with reset link
3. Link redirects to `reset-password.html` with tokens in URL hash
4. Page extracts tokens and establishes Supabase session
5. User enters new password
6. Page calls `supabase.auth.updateUser()` to update password
7. User can now sign in with new password

## Testing

1. Request password reset from your app
2. Check email for reset link
3. Click link - should open `reset-password.html`
4. Enter new password (minimum 6 characters)
5. Confirm password
6. Click "Reset Password"
7. Should see success message
8. Try signing in with new password

## Troubleshooting

**Page shows "Invalid or missing reset link":**
- Check that tokens are in URL hash (`#access_token=...&refresh_token=...`)
- Verify Supabase redirect URLs are configured correctly

**"Failed to establish session" error:**
- Token may be expired (tokens expire after 1 hour)
- Request a new password reset

**Password update fails:**
- Check browser console for errors
- Verify Supabase credentials are correct
- Ensure password meets requirements (6+ characters)

## Security Notes

- ✅ Anon key is safe to expose (designed for client-side use)
- ✅ Tokens are time-limited (1 hour default)
- ✅ No service role key needed
- ✅ Same security as server-side approach
