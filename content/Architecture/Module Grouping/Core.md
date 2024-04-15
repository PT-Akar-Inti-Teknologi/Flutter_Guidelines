The Core group houses modules that are fundamental to the application's functionality and are intended for global use throughout the app. These modules often include essential features, such as  localization, reusable component & Utils method, Typography, and theme management. to avoid circular dependency core modules should not be depend with modules outside of the core group. Example :  (Core should note depend on Payment Module)

```
core/components/lib/src/
├── component_assets.dart
├── const
│   ├── asset_image.dart
│   ├── const.dart
│   ├── di.dart
│   ├── failure.dart
│   ├── navigation_key.dart
│   ├── pakuwon_styles.dart
│   └── schema_error_codes.dart
├── themes
│   ├── design_theme_extended.dart
│   └── design_theme_extended.theme.dart
├── use_case
│   ├── change_language_uc.dart
│   └── usecases.dart
├── utils
│   ├── api_response
│   ├── extension
│   ├── get_current_os.dart
│   ├── typedef
│   ├── utils.dart
│   └── validators
└── widgets
    ├── atoms
    ├── base_scaffold
    ├── custom_toast.dart
    ├── design_app_bar.dart
    ├── formx
    ├── languange_switch.dart
    ├── pdf_view.dart
    ├── separator.dart
    └── widgets.dart

```
*example of Core Module*

