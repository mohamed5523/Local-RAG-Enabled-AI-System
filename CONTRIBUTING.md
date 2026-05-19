# 🤝 Contributing Guidelines

Thank you for your interest in contributing to LLM_AI! This document provides guidelines for contributing.

---

## 📋 Getting Started

### 1. Fork & Clone
```bash
# Fork the repository on GitHub
# Clone your fork
git clone https://github.com/yourusername/LLM_AI.git
cd LLM_AI

# Add upstream
git remote add upstream https://github.com/original/LLM_AI.git
```

### 2. Create Branch
```bash
# Update main
git fetch upstream
git checkout main
git merge upstream/main

# Create feature branch
git checkout -b feature/your-feature-name
# or for bug fixes
git checkout -b bugfix/your-bug-name
```

### 3. Development Setup
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows

# Install dependencies
cd all
pip install -r requirements.txt

# Install development dependencies (if available)
pip install pytest black flake8 mypy
```

---

## 💻 Code Guidelines

### Python Style
```python
# Follow PEP 8
# - 4 spaces indentation
# - max line length 88 (Black formatter)
# - Use type hints where possible

def search_documents(query: str, top_k: int = 5) -> List[Dict]:
    """
    Search documents using RAG.
    
    Args:
        query: Search query string
        top_k: Number of results to return
        
    Returns:
        List of matching document chunks
    """
    pass
```

### Naming Conventions
```
├─ Functions: snake_case
├─ Classes: PascalCase
├─ Constants: UPPER_SNAKE_CASE
├─ Private methods: _leading_underscore
└─ Avoid single letter variables (except i, j in loops)
```

### Comments & Documentation
```python
# Use docstrings for all public functions
# Use type hints
# Comment complex logic, not obvious code

# ❌ Bad
x = y + z  # add y and z

# ✅ Good
total_embedding_dimension = chunk_embedding + query_embedding
```

---

## 🧪 Testing

### Unit Tests
```bash
# Run tests
pytest tests/

# Run with coverage
pytest --cov=. tests/

# Run specific test
pytest tests/test_search.py::test_search_documents
```

### Test Structure
```python
# tests/test_main.py
import pytest
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_search_documents():
    """Test document search functionality."""
    response = client.post(
        "/search",
        json={"query": "test", "top_k": 5}
    )
    assert response.status_code == 200
    assert len(response.json()) <= 5
```

### Test Coverage
- Aim for > 80% code coverage
- Test happy paths and edge cases
- Test error handling

---

## 📝 Commit Messages

### Format
```
<type>: <subject>

<body>

<footer>
```

### Types
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code style (formatting, etc.)
- `refactor:` Code refactoring
- `perf:` Performance improvements
- `test:` Test additions/changes
- `chore:` Build, deps, tooling

### Examples
```bash
git commit -m "feat: add support for DOCX files"
git commit -m "fix: handle missing embeddings gracefully"
git commit -m "docs: update API documentation"
git commit -m "refactor: simplify vector search logic"
```

---

## 🔄 Pull Request Process

### Before Submitting
1. **Update from upstream**
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Run tests**
   ```bash
   pytest tests/
   ```

3. **Format code**
   ```bash
   black . --line-length 88
   flake8 .
   mypy .
   ```

4. **Check for conflicts**
   ```bash
   git push origin feature/your-feature
   ```

### PR Description Template
```markdown
## Description
Brief description of changes

## Related Issues
Fixes #123

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Breaking change

## Testing
- [ ] Added unit tests
- [ ] Tested locally
- [ ] Manual testing performed

## Checklist
- [ ] Code follows style guidelines
- [ ] Commented complex logic
- [ ] Updated documentation
- [ ] Tests pass locally
```

---

## 🐛 Bug Reports

### Issue Template
```markdown
## Description
Clear description of the bug

## Steps to Reproduce
1. ...
2. ...
3. ...

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS: [Windows/Mac/Linux]
- Python version: 3.11
- Docker version: 24.0
- Ollama version: latest

## Error Messages
```
paste error trace
```

## Screenshots
[if applicable]
```

---

## ✨ Feature Requests

### Feature Proposal Template
```markdown
## Problem Statement
What problem does this solve?

## Proposed Solution
How should it work?

## Alternatives Considered
Other approaches?

## Additional Context
Why is this important?

## Example Usage
```python
# How would users use this?
```

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

---

## 📦 Adding Dependencies

### Update requirements.txt
```bash
# Add to requirements.txt manually
pip-audit new-package==1.0.0

# Or use pip-tools
pip-compile requirements.in > requirements.txt
```

### Guidelines
- Only add necessary dependencies
- Prefer mature, well-maintained packages
- Document why dependency is needed
- Consider security implications

---

## 🚀 Release Process

### Version Numbering
Follow [Semantic Versioning](https://semver.org/):
- MAJOR.MINOR.PATCH (e.g., 1.2.3)
- MAJOR: Breaking changes
- MINOR: New features (backward compatible)
- PATCH: Bug fixes

### Release Checklist
- [ ] All tests pass
- [ ] Update CHANGELOG.md
- [ ] Update version numbers
- [ ] Create git tag
- [ ] Create GitHub release

---

## 📚 Documentation

### Adding Documentation
1. Use markdown format
2. Include code examples
3. Add table of contents for long docs
4. Update README if relevant

### Documentation Style
```markdown
# Main Title

## Subsection

### Sub-subsection

Brief explanation.

### Code Example
```python
code here
```

### Tables
| Column 1 | Column 2 |
|----------|----------|
| Value    | Value    |
```

---

## 🎨 Code Review

### What Reviewers Look For
- ✅ Follows style guidelines
- ✅ Adequate tests
- ✅ Clear documentation
- ✅ No breaking changes (unless intended)
- ✅ Performance implications
- ✅ Security considerations

### Responding to Feedback
- Be respectful and constructive
- Discuss disagreements
- Update PR after addressing feedback
- Re-request review when ready

---

## 🔒 Security

### Security Issues
⚠️ **Do NOT open public issues for security vulnerabilities**

1. Email: security@example.com
2. Include: Description, reproduction steps, impact
3. Allow time for response before disclosure

### Security Best Practices
- Never commit secrets or credentials
- Use `.env.example` for template
- Sanitize user input
- Validate file uploads
- Use parameterized queries
- Keep dependencies updated

---

## 📖 Resources

- [PEP 8 Style Guide](https://pep8.org/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [MilvusDB Documentation](https://milvus.io/)
- [Ollama Documentation](https://ollama.ai/)

---

## 🎓 Learning Resources

- Setup development environment: see [SETUP.md](./docs/SETUP.md)
- Architecture overview: see [ARCHITECTURE.md](./docs/ARCHITECTURE.md)
- API details: see [API_DOCUMENTATION.md](./docs/API_DOCUMENTATION.md)

---

## ❓ Questions?

- Check existing [GitHub Issues](https://github.com/yourusername/LLM_AI/issues)
- Create a [Discussion](https://github.com/yourusername/LLM_AI/discussions)
- Join our community chat

---

## 🙏 Thank You!

Thank you for contributing to LLM_AI! Your effort helps make this project better for everyone.

---

Last updated: 2025-12-24
