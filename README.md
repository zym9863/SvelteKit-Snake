# 🐍 SvelteKit Snake Game

[English](README.md) | [简体中文](README_zh.md)

A classic Snake game built with SvelteKit and TypeScript. This modern implementation features smooth animations, progressive difficulty, and local high score tracking.

![Snake Game](https://img.shields.io/badge/SvelteKit-v2.22.0-ff3c00?style=flat-square&logo=svelte)
![TypeScript](https://img.shields.io/badge/TypeScript-v5.0.0-blue?style=flat-square&logo=typescript)
![Svelte](https://img.shields.io/badge/Svelte-v5.0.0-ff3c00?style=flat-square&logo=svelte)

## 🎮 Features

- **Classic Gameplay**: Traditional snake mechanics with modern polish
- **Responsive Controls**: Support for both WASD and arrow keys
- **Progressive Difficulty**: Game speed increases as you collect more food
- **Score System**: Track your current score and persistent high score
- **Pause/Resume**: Press spacebar to pause the game at any time
- **Smooth Animations**: Food pulses and smooth snake movement
- **Responsive Design**: Works on both desktop and mobile devices
- **Local Storage**: High scores are saved locally

## 🚀 Getting Started

### Prerequisites

- Node.js (v16 or higher)
- npm, yarn, or pnpm

### Installation

1. Clone the repository:
```bash
git clone https://github.com/zym9863/sveltekit-snake.git
cd sveltekit-snake
```

2. Install dependencies:
```bash
npm install
# or
pnpm install
# or
yarn install
```

3. Start the development server:
```bash
npm run dev
# or start and open in browser
npm run dev -- --open
```

## 🎯 How to Play

1. **Start**: Press the "开始游戏" button or spacebar to begin
2. **Control**: Use WASD or arrow keys to control the snake
   - **W/↑**: Move up
   - **S/↓**: Move down
   - **A/←**: Move left
   - **D/→**: Move right
3. **Pause**: Press spacebar during gameplay to pause/resume
4. **Objective**: Eat the red food to grow and increase your score
5. **Avoid**: Don't hit the walls or your own tail!

## 🏗️ Project Structure

```
sveltekit-snake/
├── src/
│   ├── lib/
│   │   └── Snake.svelte    # Main game component
│   ├── routes/
│   │   ├── +layout.svelte  # Root layout
│   │   └── +page.svelte    # Home page
│   └── app.d.ts           # TypeScript declarations
├── static/                 # Static assets
├── package.json           # Project dependencies
├── svelte.config.js       # SvelteKit configuration
├── tsconfig.json          # TypeScript configuration
└── vite.config.ts         # Vite configuration
```

## 🛠️ Technical Details

### Game Configuration

- **Grid Size**: 20x20 cells
- **Cell Size**: 20px
- **Initial Speed**: 150ms per move
- **Speed Increment**: 10ms faster per food eaten
- **Minimum Speed**: 50ms (maximum difficulty)

### Key Technologies

- **SvelteKit**: Full-stack framework for building web applications
- **TypeScript**: Type-safe JavaScript for better developer experience
- **Svelte 5**: Latest version with enhanced reactivity
- **Vite**: Fast build tool and development server

## 📦 Building for Production

To create a production build:

```bash
npm run build
```

Preview the production build:

```bash
npm run preview
```

## 🚢 Deployment

This project uses the auto adapter by default, which automatically detects and uses the correct adapter for your deployment platform. For specific deployment targets, you may need to install and configure a different [adapter](https://svelte.dev/docs/kit/adapters).

### Example deployment options:

- **Vercel**: Install `@sveltejs/adapter-vercel`
- **Netlify**: Install `@sveltejs/adapter-netlify`
- **Node.js**: Install `@sveltejs/adapter-node`
- **Static**: Install `@sveltejs/adapter-static`

## 📝 Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run check` - Run svelte-check for type checking
- `npm run check:watch` - Run svelte-check in watch mode

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 🎨 Future Enhancements

- [ ] Multiple difficulty levels
- [ ] Different game modes
- [ ] Power-ups and special foods
- [ ] Multiplayer support
- [ ] Touch controls for mobile
- [ ] Sound effects and background music
- [ ] Leaderboard system
- [ ] Theme customization
