## 概述

編譯選項可以在使用MSBuild的項目裡通過MSBuild屬性指定。

## 例子

```XML
  <PropertyGroup Condition="'$(Configuration)' == 'Debug'">
    <TypeScriptRemoveComments>false</TypeScriptRemoveComments>
    <TypeScriptSourceMap>true</TypeScriptSourceMap>
  </PropertyGroup>
  <PropertyGroup Condition="'$(Configuration)' == 'Release'">
    <TypeScriptRemoveComments>true</TypeScriptRemoveComments>
    <TypeScriptSourceMap>false</TypeScriptSourceMap>
  </PropertyGroup>
  <Import Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets"
          Condition="Exists('$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets')" />
```

## 對映

編譯選項                                      | MSBuild屬性名稱                             | 可用值
---------------------------------------------|--------------------------------------------|-----------------
`--declaration`                              | TypeScriptGeneratesDeclarations            | 布爾值
`--module`                                   | TypeScriptModuleKind                       | `AMD`, `CommonJs`, `UMD` 或 `System`
`--target`                                   | TypeScriptTarget                           | `ES3`, `ES5`, or `ES6`
`--charset`                                  | TypeScriptCharset                          |
`--emitBOM`                                  | TypeScriptEmitBOM                          | 布爾值
`--emitDecoratorMetadata`                    | TypeScriptEmitDecoratorMetadata            | 布爾值
`--experimentalDecorators`                   | TypeScriptExperimentalDecorators           | 布爾值
`--inlineSourceMap`                          | TypeScriptInlineSourceMap                  | 布爾值
`--inlineSources`                            | TypeScriptInlineSources                    | 布爾值
`--locale`                                   | *自動的*                                    | 自動設定成PreferredUILang的值
`--mapRoot`                                  | TypeScriptMapRoot                          | 檔案路徑
`--newLine`                                  | TypeScriptNewLine                          | `CRLF` 或 `LF`
`--noEmitOnError`                            | TypeScriptNoEmitOnError                    | 布爾值
`--noEmitHelpers`                            | TypeScriptNoEmitHelpers                    | 布爾值
`--noImplicitAny`                            | TypeScriptNoImplicitAny                    | 布爾值
`--noLib`                                    | TypeScriptNoLib                            | 布爾值
`--noResolve`                                | TypeScriptNoResolve                        | 布爾值
`--out`                                      | TypeScriptOutFile                          | 檔案路徑
`--outDir`                                   | TypeScriptOutDir                           | 檔案路徑
`--preserveConstEnums`                       | TypeScriptPreserveConstEnums               | 布爾值
`--removeComments`                           | TypeScriptRemoveComments                   | 布爾值
`--rootDir`                                  | TypeScriptRootDir                          | 檔案路徑
`--isolatedModules`                          | TypeScriptIsolatedModules                  | 布爾值
`--sourceMap`                                | TypeScriptSourceMap                        | 檔案路徑
`--sourceRoot`                               | TypeScriptSourceRoot                       | 檔案路徑
`--suppressImplicitAnyIndexErrors`           | TypeScriptSuppressImplicitAnyIndexErrors   | 布爾值
`--suppressExcessPropertyErrors`             |  TypeScriptSuppressExcessPropertyErrors    | 布爾值
`--moduleResolution`                         | TypeScriptModuleResolution                 | `Classic` or `NodeJs`
`--jsx`                                      | TypeScriptJSXEmit                          | `React` or `Preserve`
`--project`                                  | *VS不支援*                                  |
`--watch`                                    | *VS不支援*                                  |
`--diagnostics`                              | *VS不支援*                                  |
`--listFiles`                                | *VS不支援*                                  |
`--noEmit`                                   | *VS不支援*                                  |
*VS特有選項*                                  | TypeScriptAdditionalFlags                  | *任意編譯選項*


## 我使用的Visual Studio版本里支援哪些選項

查詢 `C:\Program Files (x86)\MSBuild\Microsoft\VisualStudio\v$(VisualStudioVersion)\TypeScript\Microsoft.TypeScript.targets` 檔案。
可用的MSBuild XML標籤與相應的`tsc`編譯選項的對映都在那裡。