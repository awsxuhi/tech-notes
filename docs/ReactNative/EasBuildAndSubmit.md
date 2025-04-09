下面是一份优化后的知识点总结，整理了使用 EAS 构建与提交 iOS 应用的两种方式，以及各自的优缺点和详细步骤：

---

## 使用 EAS 进行构建与提交 iOS 应用

### 1. 云上构建方式

**优点：**

- 不占用本地资源
- 构建流程完全在 EAS 云端完成

**缺点：**

- 每月只有 15 次免费构建额度
- 每次构建需要进入队列等待，构建时间可能较长

**具体步骤：**

1. **创建 Credentials**  
   通过 EAS 帮助在 App Store Connect 上自动创建 App ID、证书和 Provisioning Profile。  
   执行命令：

   ```bash
   eas credentials -p ios
   ```

2. **构建应用**  
   使用 production 配置在云端构建 iOS 应用。  
   执行命令：

   ```bash
   eas build --platform ios --profile production
   ```

   构建完成后，会在 EAS 云端生成 ipa 文件。

3. **提交到 App Store Connect**  
   构建完成后，直接将 ipa 文件提交到 App Store Connect。  
   执行命令：
   ```bash
   eas submit --platform ios --profile production
   ```

---

### 2. 本地构建方式

**优点：**

- 构建速度较快，约 10 分钟完成构建
- 提交速度快，约 5 分钟完成
- 更灵活，不用受云构建额度和队列影响

**缺点：**

- 需要占用本地资源
- 要求本地环境配置完整（如 macOS 环境，Xcode 等）

**具体步骤：**

1. **不需要执行预构建原生工程（如需）**  
   根本不需要执行下面的命令，因为 eas build 和 eas submit 都不会在项目的目录下创建 ios 目录，我相信它是在另外的目录下创建了内容，例如“~/Library/Developer/Xcode/DerivedData/”

   ```bash
   eas prebuild --platform ios
   npx expo prebuild --clean
   ```

2. **本地构建应用**  
   在本地进行构建，生成 ipa 文件。  
   执行命令：

   ```bash
   eas build --platform ios --profile production --local
   ```

   构建完成后，会在项目根目录下生成形如 `build-xxxx.ipa` 的文件。

3. **提交 ipa 文件**  
   直接使用生成的 ipa 文件进行提交，不必登录 App Store Connect。  
   执行命令：
   ```bash
   eas submit --platform ios --profile production --path ./build-1742288959326.ipa
   ```
   提交成功后，只需在 iPhone 上打开 TestFlight，不断刷新便能看到最新的更新，然后点击“更新”即可进行本地安装。

---

### 总结与经验

- **云上构建适合：** 不希望占用本地资源，且不介意等待队列的场景；适合构建次数较少的情况，但需注意每月免费额度限制。
- **本地构建适合：** 追求快速构建和提交体验的开发场景；适合频繁调试和迭代，但需要确保本地环境配置正确。
- **Credential 管理：** 无论哪种方式，都建议先通过 `eas credentials -p ios` 管理好相关证书和配置，确保 App Store Connect 信息正确。
- **自动更新 TestFlight：** 一旦提交成功，无需登录 App Store Connect，通过 TestFlight 刷新即可获取最新的安装包。

以上步骤和经验可以作为未来使用 EAS 构建和提交 iOS 应用的参考指南，根据具体项目需求选择合适的构建方式。

最后清理构建缓存数据的命令如下。

```
rm -rf ~/Library/Developer/Xcode/DerivedData/* && rm -rf ios/build
```
