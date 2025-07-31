# NPM Publishing Guide

This guide will help you publish the Rustreexo WASM packages to NPM as shown on the GitHub Pages demo.

## 📦 **Package Names**

The following scoped packages will be published:
- `@rustreexo/rustreexo-wasm-web` - For web browsers
- `@rustreexo/rustreexo-wasm-nodejs` - For Node.js
- `@rustreexo/rustreexo-wasm-bundler` - For bundlers (webpack, rollup, etc.)

## 🔧 **Setup Steps**

### **Step 1: Create NPM Account and Organization**

1. **Sign up/Login to NPM**: Go to [npmjs.com](https://www.npmjs.com)
2. **Create Organization**: 
   - Go to your profile → Organizations → Create Organization
   - Name: `rustreexo` (this creates the `@rustreexo` scope)
   - Make it public (free)

### **Step 2: Generate NPM Token**

```bash
# Login to NPM CLI
npm login

# Generate automation token
npm token create --type=automation --read-only=false
```

Copy the generated token (starts with `npm_`).

### **Step 3: Add Token to GitHub**

1. Go to your repository: `Settings` → `Secrets and variables` → `Actions`
2. Click `New repository secret`
3. **Name**: `NPM_TOKEN`
4. **Value**: Paste your NPM automation token
5. Click `Add secret`

### **Step 4: Create Your First Release**

```bash
# Create and push a release tag
git tag v0.1.0
git push origin v0.1.0
```

This will automatically trigger the release workflow which will:
- Build all WASM targets
- Update package.json files with correct metadata
- Publish to NPM with proper scoped names
- Create a GitHub release

## 🚀 **What Happens During Release**

1. **Build Phase**: Creates WASM packages for all targets
2. **Package Update**: Sets correct scoped names and metadata
3. **NPM Publishing**: Publishes each package with `--access public`
4. **GitHub Release**: Creates release with download links and checksums

## 📋 **Verify Release**

After the release workflow completes:

1. **Check NPM packages**:
   ```bash
   npm view @rustreexo/rustreexo-wasm-web
   npm view @rustreexo/rustreexo-wasm-nodejs
   npm view @rustreexo/rustreexo-wasm-bundler
   ```

2. **Test installation**:
   ```bash
   npm install @rustreexo/rustreexo-wasm-web
   ```

3. **Check GitHub release**: Go to your repository's Releases page

## 🔄 **Future Releases**

For subsequent releases:

```bash
# Update version in Cargo.toml
# Commit your changes
git add .
git commit -m "Release v0.2.0"

# Create and push tag
git tag v0.2.0
git push origin v0.2.0
```

## 🎯 **Package Usage**

Once published, users can install the packages as shown on your GitHub Pages demo:

```bash
# For web browsers
npm install @rustreexo/rustreexo-wasm-web

# For Node.js
npm install @rustreexo/rustreexo-wasm-nodejs

# For bundlers
npm install @rustreexo/rustreexo-wasm-bundler
```

## 🔍 **Troubleshooting**

### **Common Issues**:

1. **"Package not found"**: Make sure the `@rustreexo` organization exists and you have publish rights
2. **"Unauthorized"**: Check that your NPM_TOKEN is correct and has publish permissions
3. **"Version already exists"**: You can't republish the same version, increment the version number

### **Debug Commands**:

```bash
# Check if you're logged in to NPM
npm whoami

# Check organization access
npm access list packages @rustreexo

# Test publish (dry run)
npm publish --dry-run --access public
```

## ✅ **Ready to Publish!**

Once you've completed Steps 1-3, simply create a release tag to publish your first version to NPM! 🎉