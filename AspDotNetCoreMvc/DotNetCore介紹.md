# .NET Core 簡介

.NET Core 是一個由 Microsoft 主導的的全新版本跨平台方案的框架，它是一個免費、開源、通用開發平台。可在 Windows、macOS 和 Linux 作業系統上運行。

## .NET Core 特點

- **開源框架：** .NET Core 是由 Microsoft 維護的開源框架，並在 MIT 和 Apache 2 許可證下提供在 GitHub 上。  

- **跨平台：** .NET Core 可在 Windows、macOS 和 Linux 作業系統上運行。
  
- **跨架構一致性：** 在不同的指令集架構下執行程式碼時具有相同的行為，包括 x64、x86 和 ARM。
  
- **廣泛的應用範圍：** .NET Core 平台支援開發各種應用程式，如移動應用、桌面應用、Web 應用、雲端服務、物聯網、機器學習、微服務、遊戲等。

- **支援多種語言：** 您可以使用 C#、F# 和 Visual Basic 程式語言來開發 .NET Core 應用程式，並可以使用自身習慣的的集成開發環境(IDE)，包括 Visual Studio、Visual Studio Code、Sublime Text、Vim 等。

- **模組化架構：** .NET Core 支援使用 NuGet 套件的模塊化架構方法。不同的 NuGet 套件可用於添加到 .NET Core 項目中的各種功能。甚至預設的 .NET Core 库也以 NuGet 套件的形式提供。用於默認 .NET Core 應用程式模型的 NuGet 套件是 Microsoft.NETCore.App。
- **CLI 工具：** .NET Core 包括用於開發和持續集成的命令行界面（CLI）工具。
- **靈活的部署：** .NET Core 應用程式可以部署到用戶範圍、系統範圍，也可以使用 Docker 容器進行部署。
- **兼容性：** 通過使用 .NET Standard 規範，與 .NET Framework 和 Mono API 兼容。
- **高性能：** .NET Core 經過優化，具有即時（JIT）編譯等功能，可以在運行時將代碼轉譯為機器指令，提高執行速度。
  
- **統一平台：** 從 .NET Core 3.1 開始，Microsoft 將 .NET 平台統一，將 .NET Core、.NET Framework 和 Xamarin 整合成一個稱為 ".NET" 的統一平台，以實現不同應用程式類型的一致 API 和運行時行為。

## .NET Core 版本歷史

| 版本                  | Visual Studio            | 發佈日期            | 支援結束日期        |
| --------------------- | ------------------------ | ------------------- | ------------------- |
| .NET 9 -  (projected) |                          |                     |                     |
| .NET 8 - 最新版本     | Visual Studio 2022 v17.8 | 2023 年 11 月 14 日 | 2026 年 11 月 10 日 |
| .NET 7                | Visual Studio 2022 v17.4 | 2022 年 11 月 8 日  | 2024 年 5 月 14 日  |
| .NET 6 (LTS)          | Visual Studio 2022       | 2021 年 11 月 9 日  | 2024 年 11 月 12 日 |
| .NET 5                | Visual Studio 2019       | 2020 年 11 月 10 日 | 2022 年 5 月 10 日  |
| .NET Core 3.1 (LTS)   | Visual Studio 2019       | 2019 年 12 月 3 日  | 2022 年 12 月 13 日 |
| .NET Core 3.0         | Visual Studio 2019       | 2019 年 9 月 23 日  | 2020 年 3 月 3 日   |
| .NET Core 2.1 (LTS)   | Visual Studio 2017, 2019 | 2018 年 5 月 30 日  | 2021 年 8 月 21 日  |
| .NET Core 2.0         | Visual Studio 2017, 2019 | 2017 年 8 月 14 日  | 2018 年 10 月 1 日  |
| .NET Core 1.0         | Visual Studio 2017       | 2016 年 6 月 27 日  | 2019 年 6 月 27 日  |

在 .NET Core 3.1 之後，.NET Core 的名稱被更改為 .NET 5。.NET 5 將之前獨立存在的 .NET Core、.NET Framework 和 Xamarin 平台統一起來，使開發人員能夠使用單一平台開發各種應用程式類型。

## 為何選擇 .NET Core？

.NET Framework 存在一些限制。例如，它僅運行在 Windows 平台。此外，您需要使用不同的 .NET API 來開發不同的 Windows 設備，如 Windows 桌面、Windows 商店、Windows Phone 和 Web 應用程式。此外，.NET Framework 是一個全局框架，對其進行的任何更改都會影響所有依賴它

的應用程式。

今天，常常需要一個可以在多種設備上運行的應用程式；例如，Web 伺服器上的後端、Windows 桌面上的管理前端、針對消費者的 Web 和移動應用程式。因此，需要一個可以在各處都運作的單一框架。基於這一考慮，Microsoft 創建了 .NET Core。.NET Core 的主要目標是將 .NET Framework 開源化，實現跨平台兼容，並可在從數據中心到觸控設備等各種垂直領域中使用。

## 結論

.NET Core 是一個強大而多功能的開發平台，旨在滿足現代軟件開發的需求，包括跨平台開發和不同的應用領域。它具有開源特性、高性能、跨平台兼容性和模塊化架構，使開發人員能夠輕鬆構建各種應用程式。
