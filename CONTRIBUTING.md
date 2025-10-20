# Contributing to Trip Snapshot Generator

Thank you for considering contributing! This project welcomes contributions from the community.

## How to Contribute

### Reporting Bugs

1. Check if the issue already exists in [GitHub Issues](https://github.com/Kr8thor/trip-snapshot-n8n/issues)
2. If not, create a new issue with:
   - Clear description of the bug
   - Steps to reproduce
   - Expected vs actual behavior
   - n8n version and environment details

### Suggesting Enhancements

1. Open a new issue with the `enhancement` label
2. Describe your idea clearly
3. Explain why this enhancement would be useful
4. If possible, provide examples or mockups

### Pull Requests

1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature-name`
3. Make your changes
4. Test thoroughly with different city pairs and edge cases
5. Update documentation if needed
6. Commit with clear messages: `git commit -m "feat: add toll estimation"`
7. Push to your fork: `git push origin feature/your-feature-name`
8. Open a Pull Request

## Development Guidelines

### Code Style

- Use clear, descriptive variable names
- Comment complex logic
- Keep functions focused and single-purpose
- Follow existing formatting conventions

### Testing Your Changes

1. Import the modified workflow into n8n
2. Test with various inputs:
   - Different city pairs (domestic and international)
   - Edge cases (1 day trips, 10 day trips)
   - Invalid inputs (missing params, non-existent cities)
3. Verify all outputs:
   - Map renders correctly
   - Weather data is accurate
   - Calculations are correct

### Documentation

- Update README.md if you add features
- Add inline comments for complex code
- Include examples in the documentation

## Ideas for Contributions

### High Priority

- [ ] Error handling improvements
- [ ] Better rate limiting for public APIs
- [ ] Caching layer for geocoding results
- [ ] Mobile-responsive design improvements

### New Features

- [ ] Multi-stop route support (A → B → C)
- [ ] Alternative routes (fastest vs scenic)
- [ ] Public transit integration
- [ ] Historical weather comparison
- [ ] PDF export functionality
- [ ] Toll cost estimation (via TollGuru API)
- [ ] Carbon footprint calculator
- [ ] Real-time traffic overlay
- [ ] Hotel/restaurant suggestions along route
- [ ] Integration with booking systems

### Integrations

- [ ] Slack bot commands
- [ ] Discord webhook
- [ ] Telegram bot
- [ ] SMS notifications via Twilio
- [ ] Google Sheets logging
- [ ] Airtable integration

### Documentation

- [ ] Video tutorial
- [ ] More customization examples
- [ ] Production deployment guide
- [ ] API rate limit handling guide
- [ ] Troubleshooting section

## Questions?

Feel free to:
- Open a [Discussion](https://github.com/Kr8thor/trip-snapshot-n8n/discussions)
- Ask in the [n8n Community](https://community.n8n.io)
- Reach out via GitHub Issues

## Code of Conduct

Be respectful, inclusive, and constructive. We're all here to build cool automation together!

---

**Thank you for contributing! 🙏**