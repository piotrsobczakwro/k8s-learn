# Contributing to k8s-learn

Thank you for your interest in improving this Kubernetes learning path! While this is primarily a personal learning repository, contributions are welcome.

## How to Contribute

### 1. Report Issues
Found a typo, broken link, or outdated information?
- Open an issue describing the problem
- Include the lesson/file name
- Suggest a fix if possible

### 2. Suggest Improvements
Have ideas for better explanations or examples?
- Open an issue with your suggestion
- Explain why it would improve the learning experience
- Provide examples if applicable

### 3. Submit Pull Requests
Want to contribute content directly?

**Before you start:**
- Check existing issues and PRs
- Open an issue to discuss major changes
- Ensure your contribution aligns with the learning path structure

**Guidelines:**
- Follow the existing format and style
- Keep explanations clear and beginner-friendly
- Include practical examples
- Test all YAML files
- Update relevant documentation

### 4. Add Examples
Contributing YAML examples?
- Ensure they work with current Kubernetes versions
- Include comments explaining key concepts
- Test in a local cluster (Minikube/Kind)
- Place in appropriate lesson's `examples/` directory

## Content Standards

### Writing Style
- **Clear and concise**: Avoid jargon when possible
- **Progressive complexity**: Build on previous concepts
- **Practical focus**: Include real-world examples
- **Consistent formatting**: Follow existing structure

### Code Examples
- **Working code**: Test all YAML files
- **Well-commented**: Explain non-obvious parts
- **Best practices**: Follow Kubernetes recommendations
- **Version-aware**: Note Kubernetes version requirements

### Documentation Structure
```markdown
# Title

Brief introduction (1-2 paragraphs)

## Main Concept

Explanation with examples

### Subsection

Details

## Hands-On Example

Practical YAML or commands

## Best Practices

✅ Do this
❌ Avoid that

## Key Takeaways

Bullet points of main lessons

---

[← Previous](link) | [Next →](link)
```

## Types of Contributions Needed

### High Priority
- 🔴 Corrections to technical errors
- 🟠 Updates for new Kubernetes versions
- 🟡 Additional practical examples
- 🟢 Improved explanations of complex topics

### Welcome Additions
- More exercises with solutions
- Video tutorial links
- Troubleshooting guides
- Real-world case studies
- Translation to other languages

### Not Needed
- Major restructuring (open issue first)
- Promotional content
- Off-topic resources

## File Organization

```
lessons/
  XX-lesson-name/
    README.md              # Lesson overview
    01-topic-name.md       # Individual topics
    02-another-topic.md
    examples/
      example-1.yaml       # Working YAML files
      example-2.yaml
```

## Testing Your Changes

### For Documentation
1. Check spelling and grammar
2. Verify all links work
3. Ensure formatting is consistent
4. Preview markdown rendering

### For YAML Files
1. Validate YAML syntax
2. Test in local Kubernetes cluster
3. Verify expected behavior
4. Include comments

```bash
# Validate YAML
kubectl apply --dry-run=client -f example.yaml

# Test in cluster
kubectl apply -f example.yaml
kubectl get all
kubectl delete -f example.yaml
```

## Commit Messages

Use clear, descriptive commit messages:

```
Good:
- "Add StatefulSet example for MySQL deployment"
- "Fix typo in networking lesson"
- "Update HPA example for v1.28"

Avoid:
- "Update"
- "Fix stuff"
- "Changes"
```

## Pull Request Process

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b improve-lesson-3`)
3. **Make** your changes
4. **Test** your changes
5. **Commit** with clear messages
6. **Push** to your fork
7. **Open** a Pull Request

**PR Description should include:**
- What changes were made
- Why the changes improve the learning path
- Which lesson(s) are affected
- Testing performed

## Code of Conduct

### Be Respectful
- Be kind and courteous
- Respect different perspectives
- Accept constructive criticism

### Be Helpful
- Help beginners learn
- Share knowledge generously
- Provide constructive feedback

### Be Patient
- Remember everyone is learning
- Explain concepts clearly
- Encourage questions

## Recognition

Contributors will be acknowledged in:
- Pull request comments
- Commit history
- Future CONTRIBUTORS.md file (if created)

## Questions?

Not sure about something?
- Open an issue with your question
- Reach out to the maintainer
- Check existing issues and PRs

## License

By contributing, you agree that your contributions will be licensed under the same license as this project.

---

**Thank you for helping make Kubernetes more accessible to learners! 🚀**
