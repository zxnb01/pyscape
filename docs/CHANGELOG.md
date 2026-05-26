# Changelog

All notable changes to PyScape-Basic are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- Real-time code duel system with WebSocket support
- Multi-language code sandbox (Python, JavaScript, Java, C++)
- Algorithm visualizers (sorting, pathfinding, K-means, neural networks, gradient descent)
- Adaptive learning roadmap generation based on interests
- Portfolio generation and PDF export
- User gamification system (XP, levels, badges)
- Leaderboard rankings
- Duel matchmaking system
- ML Sandbox with code execution
- Real-time learning engagement dashboard
- News feed integration

### Changed
- Reorganized project folder structure for clarity
- Improved documentation with architecture guide
- Enhanced code organization with feature-based pages

### Fixed
- Environment variable handling
- Documentation consolidation

## [0.1.0] - 2025-06-01

### Initial Release

**Core Features:**
- ✅ Adaptive learning platform with personalized paths
- ✅ Algorithm visualizer with step-by-step visualization
- ✅ Project labs with guided practical projects
- ✅ Real-time code duels (1v1 competitive coding)
- ✅ Real-time learning engagement dashboard
- ✅ ML Sandbox with multi-language code execution
- ✅ Portfolio view and PDF export
- ✅ Algorithm visualizers (6 types)

**Tech Stack:**
- React.js + Tailwind CSS + Framer Motion
- Express.js backend + Supabase PostgreSQL
- WebSocket for real-time features
- Judge0 API for code execution
- Supabase authentication

**Documentation:**
- README with feature overview
- Development setup guide
- API documentation
- Backend README

---

## Version Guidelines

### Version Numbers

Use semantic versioning: `MAJOR.MINOR.PATCH`

- **MAJOR** — Breaking changes
- **MINOR** — New features (backward compatible)
- **PATCH** — Bug fixes

### Release Process

1. Update version in `package.json`
2. Update this CHANGELOG.md
3. Create git tag: `git tag v0.1.0`
4. Push changes and tag: `git push && git push --tags`
5. Create GitHub Release with CHANGELOG notes

### Maintenance

- Maintain this file for each release
- Keep `[Unreleased]` section updated during development
- Archive old versions in reverse chronological order

---

## Roadmap

### Phase 2 (Post-MVP)
- [ ] Advanced matchmaking (skill-based rating system)
- [ ] Multiple project labs and progressive difficulty
- [ ] AI tutor chat with personalized guidance
- [ ] Social features (peer code review, discussions)
- [ ] Mobile app (React Native)
- [ ] Advanced visualizers and interactive tutorials

### Research Variants
- [ ] Multi-agent orchestration (pyscape-multi-agent)
- [ ] Research evaluation framework (pyscape-research)
- [ ] Comparative benchmarks across variants

---

For more details, see:
- [DEVELOPMENT.md](./docs/DEVELOPMENT.md) — Setup and running
- [ARCHITECTURE.md](./docs/ARCHITECTURE.md) — System design
