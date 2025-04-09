**问题与现象**

- **问题描述**  
  我在使用 Expo 开发一个 React Native 项目时，发现通过 `npm install prettier --save-dev` 安装的 Prettier 并没有出现在 `node_modules` 目录里。相反，通过 `npx expo install prettier` 安装后却可以在 `node_modules` 中看到该目录。尝试 `npx expo install prettier --dev` 或 `npm install prettier --save-dev` 也遇到同样情况：依旧看不到 `prettier` 目录。 使用 cursor 的时候，在 IDE 的右下角出现红色的 Prettier 警告提示。

- **疑问**
  1. 是否只能通过 `npx expo install prettier` 来成功安装并看到 `prettier` 目录？
  2. 如果把 Prettier 安装在 `dependencies` 里，最终会不会导致打包出来的 ipa 文件体积变大？

---

**分析与原因**

1. **expo install 与 npm install 的区别**

   - **expo install**
     - 会根据当前 Expo SDK 版本选择兼容版本
     - 默认将依赖写入到 `dependencies`
     - 可以保证运行时所有必需的依赖都被正确识别并安装
   - **npm install --save-dev / --dev**
     - 将依赖写入到 `devDependencies`
     - 一般来说，Prettier 这样的格式化工具确实属于开发依赖
     - 但在 Expo 项目中，部分工具/包在打包或依赖管理时可能不会被正确处理或显示

2. **为什么在 node_modules 中看不到 Prettier**

   - 通过 `npm install --save-dev` 安装的 Prettier，因其在运行时不被直接引用，Expo 可能对其做了优化或未将其列入可见依赖
   - 通过 `npx expo install prettier` 安装后，Prettier 被写入到 `dependencies`，因此能够在 `node_modules` 中直接看到对应文件夹

3. **对最终构建体积的影响**
   - Prettier 是开发工具，不会在应用运行时被引用
   - 即便放在 `dependencies` 中，Expo 的打包工具（Metro bundler）只会打包实际用到的代码
   - 因此不会显著增加最终的 ipa 文件体积

---

**建议**

- 对于 Expo 项目，建议使用 **`expo install`** 来安装所有依赖，包括本该属于开发依赖的工具
- 这样做可以保证版本兼容性和项目在运行时的稳定性，不必过于担心对最终构建体积的影响

---

**解决方案**

1. **卸载已安装的 Prettier**

   ```bash
   npm uninstall prettier
   ```

   确保 `package.json` 中的 `devDependencies` 里不再包含 `prettier`

2. **使用 expo install 重新安装**

   ```bash
   npx expo install prettier
   ```

   这样 Prettier 会被写入 `dependencies`，并在 `node_modules` 中正确显示

3. **验证**
   - 查看 `node_modules` 中是否出现 `prettier` 文件夹
   - 确认 `package.json` 中 `dependencies` 是否包含 `"prettier"`

通过以上步骤，你就能在 Expo 项目中正常使用 Prettier，而且不用担心它会显著增大最终的打包体积。
