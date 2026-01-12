# GitHub Repository Setup Instructions

Follow these steps to create and publish this repository to your GitHub account.

## Step 1: Create Repository on GitHub

1. Go to https://github.com/new
2. Repository name: `meeting-transcript-ontology`
3. Description: "A comprehensive SHACL 1.2 ontology for converting meeting transcriptions into structured RDF knowledge graphs"
4. Choose: **Public** (or Private if you prefer)
5. **DO NOT** initialize with README, .gitignore, or license (we already have these)
6. Click "Create repository"

## Step 2: Initialize Local Repository

Open your terminal and navigate to the project directory, then run:

```bash
cd /path/to/meeting-transcript-ontology

# Initialize git repository
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Meeting Transcript Ontology with SHACL 1.2"

# Rename branch to main (if needed)
git branch -M main
```

## Step 3: Connect to GitHub

Replace `[your-username]` with your actual GitHub username:

```bash
git remote add origin https://github.com/[your-username]/meeting-transcript-ontology.git

# Or if you use SSH:
git remote add origin git@github.com:[your-username]/meeting-transcript-ontology.git
```

## Step 4: Push to GitHub

```bash
git push -u origin main
```

You may be prompted for your GitHub credentials. If you have 2FA enabled, you'll need to use a Personal Access Token instead of your password.

## Step 5: Add Topics (Optional but Recommended)

On your GitHub repository page:

1. Click the gear icon next to "About"
2. Add topics: `semantic-web`, `rdf`, `shacl`, `knowledge-graph`, `ontology`, `meeting-intelligence`, `sparql`, `rdf-star`, `context-graphs`, `decision-tracking`
3. Click "Save changes"

## Step 6: Enable GitHub Pages (Optional)

To publish documentation:

1. Go to Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main`, folder: `/docs`
4. Click "Save"

Your documentation will be available at: `https://[your-username].github.io/meeting-transcript-ontology/`

## Step 7: Create Initial Release (Optional)

1. Go to Releases → "Create a new release"
2. Tag: `v1.0.0`
3. Title: "Initial Release: Meeting Transcript Ontology v1.0.0"
4. Description:
   ```
   First stable release of the Meeting Transcript Ontology
   
   Features:
   - Complete SHACL 1.2 ontology for meeting transcriptions
   - 11 core classes with comprehensive properties
   - 6 SHACL rules for automated inference
   - RDF-Star support for rich annotations
   - Wikidata entity linking
   - Example queries and documentation
   
   See README.md for full documentation.
   ```
5. Click "Publish release"

## File Structure You're Publishing

```
meeting-transcript-ontology/
├── README.md                          # Main documentation
├── LICENSE                            # MIT License
├── .gitignore                         # Git ignore rules
├── ontology/
│   └── meeting-transcript-ontology.ttl
├── examples/
│   ├── meeting-transcript-rdf.ttl     # Example RDF data
│   └── sample-meeting-transcript.txt  # Sample transcript
└── docs/
    ├── original-prompt.txt            # Project requirements
    ├── example-queries.md             # SPARQL query examples
    └── QUICKSTART.md                  # Quick start guide
```

## Updating README Citation

After creating the repository, update the citation in README.md:

```bibtex
@software{meeting_transcript_ontology,
  title = {Meeting Transcript Ontology: A SHACL-based Framework for Decision Tracking},
  author = {Cagle, Kurt},
  year = {2026},
  url = {https://github.com/[your-username]/meeting-transcript-ontology}
}
```

Replace `[your-username]` with your actual GitHub username.

## Common Issues

### Authentication Failed

If you get authentication errors:

1. Generate a Personal Access Token: GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. Select scopes: `repo` (all)
3. Use the token as your password when pushing

### Permission Denied (SSH)

If using SSH and getting permission denied:

1. Check your SSH key is added to GitHub: Settings → SSH and GPG keys
2. Test connection: `ssh -T git@github.com`

### Large Files Warning

If you add large files in the future (>50MB), consider using Git LFS:

```bash
git lfs install
git lfs track "*.mp4"  # example for video files
git add .gitattributes
```

## Next Steps After Publishing

1. **Add to your blog post** - Include the GitHub link in your Context Graphs blog post
2. **Share on social media** - Twitter/LinkedIn with hashtags: #SemanticWeb #KnowledgeGraphs #RDF
3. **Submit to awesome lists** - Consider submitting to awesome-semantic-web lists
4. **Enable discussions** - Settings → Features → Enable Discussions for community engagement

## Maintenance

Remember to:

- Accept pull requests that improve the ontology
- Respond to issues in a timely manner  
- Update the ontology version in releases when making changes
- Keep documentation synchronized with code changes
- Add new example queries as use cases emerge

## Questions?

If you encounter any issues with these instructions, feel free to:

1. Check GitHub's documentation: https://docs.github.com
2. Ask in GitHub Community: https://github.community
3. Open an issue in your repository once it's created
