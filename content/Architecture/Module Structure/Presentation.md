The presentation layer manages UI and state management (Cubit) codes. It includes cubit, pages, and widgets folders. Moreover, a presentation.dart file exports the presentation layer codes. Cubits and widgets within this layer may be shared across multiple pages within a module. However, if a cubit or widget is exclusive to a single page, it may be placed in its dedicated page folder (e.g., pages/login/widgets/login_button.dart, pages/login/cubit/login_cubit.dart). During refactoring, breaking down a widget from a page into its own file is recommended, utilizing part and part of.

## auth module presentation :

### What inside presentation directory :

```
packages/auth/lib/src/presentation
├── cubits
├── pages
│   ├── forgot_pin
│   ├── login
│   ├── login_pin
│   ├── pages.dart
│   ├── register
│   ├── select_taste
│   └── testing
├── presentation.dart
└── widgets

```

### What inside each Page directory :

```
packages/auth/lib/src/presentation/pages/login
├── cubit
│   ├── form
│   │   └── login_form.dart
│   ├── login_cubit.dart
│   ├── login_cubit.freezed.dart
│   └── login_state.dart
├── login_page.dart
└── widgets
    ├── login_background.dart
    └── login_bottom_sheet.dart

```

### Presentation code example

#### Main Page File :

```dart
import 'package:blur/blur.dart';
import 'package:flutter/material.dart';
import 'package:common_dependency/common_dependency.dart';

export 'cubit/login_cubit.dart';

part 'widgets/login_background.dart';
part 'widgets/login_bottom_sheet.dart';

class LoginPage extends StatelessWidget {
  const LoginPage({super.key});

  @override
  Widget build(BuildContext context) {
    return MultiBlocProvider(
      providers: [
        BlocProvider(
          create: (context) => di<LoginCubit>(),
        ),
        BlocProvider(
          create: (context) => di<BiometricCubit>(),
        ),
      ],
      child: const LoginUI(),
    );
  }
}
```

#### Refactored Widget File :

```dart
part of '../login_page.dart';

class LoginBackground extends StatelessWidget {
  const LoginBackground({
    super.key,
  });

  @override
  Widget build(BuildContext context) {
    final string = SharedStr.of(context);
    final theme = context.designThemeExtended;
    return Stack(
      children: [
        Blur(
          blur: 1.0,
          blurColor: theme.neutral400,
          colorOpacity: 0.2,
          child: DesignImage.project(
            const DesignAssetsNetwork(
              'https://www.constructionplusasia.com/wp-content/uploads/2020/12/Pakuwon-Mall-Surabaya-Pakuwon-Group.jpg',
            ),
            height: MediaQuery.of(context).size.height,
            width: double.infinity,
            fit: BoxFit.cover,
          ),
        ),
        Positioned(
          top: context.topPadding + 60,
          right: 16,
          left: 16,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.end,
            children: [
              const LanguageSwitcher().bottomMargin(24),
              _buildText(string, theme),
            ],
          ),
        )
      ],
    );
  }

  Center _buildText(SharedStr string, PakuwonTheme theme) {
    return Center(
      child: Column(
        children: [
          Text(
            string.auth_welcome_to_pg_card,
            style: MdsTextStyle.xl.overrideColor(theme.white).bold,
            textAlign: TextAlign.center,
          ),
          Text(
            string.auth_enjoy_exclusive_rewards,
            style: MdsTextStyle.sm.overrideColor(theme.white),
            textAlign: TextAlign.center,
          ),
        ],
      ),
    );
  }
}

```

#### Cubit file :

```dart
import 'package:common_dependency/common_dependency.dart';
import 'package:freezed_annotation/freezed_annotation.dart';
import 'form/login_form.dart';
part 'login_state.dart';
part 'login_cubit.freezed.dart';

class LoginCubit extends Cubit<LoginState>
    with SyncEmit<LoginState>, FormxStateManager, FormxBlocMixin<LoginState>
    implements BasePageListener<LoginState> {
  LoginCubit._({required this.formxMeta, required this.loginUseCase})
      : super(LoginState.initial(formxMeta));
  factory LoginCubit(ValidatePhoneUseCase loginUseCase) =>
      LoginCubit._(formxMeta: LoginFormx(), loginUseCase: loginUseCase);

  @override
  final FormxMeta formxMeta;
  final ValidatePhoneUseCase loginUseCase;

  void submit() async {
    syncEmit(
        (state) => state.copyWith(status: FormzStatus.submissionInProgress));
    final res = await loginUseCase(
        state.formxState.fieldValue[LoginFormx.fieldNames.phone]);
    res.fold(
      (l) {
        if (l is ValidationError) {
          for (var element in l.error) {
            setFormxErrors(
              element.field ?? '',
              [
                (_) => element.message!.message,
              ],
            );
          }
        }
        syncEmit(
          (state) => state.copyWith(
            status: FormzStatus.submissionFailure,
            failure: l,
          ),
        );
      },
      (r) {
        syncEmit(
          (state) => state.copyWith(
            status: FormzStatus.submissionSuccess,
            userLoginType: r,
          ),
        );
      },
    );
  }

  @override
  clearErrorState() {
    syncEmit((state) => state.copyWith(failure: null));
  }
}

```

#### state file :

```dart
part of 'login_cubit.dart';

@freezed
class LoginState
    with _$LoginState, FormxBlocStateMixin<LoginState>
    implements BasePageState {
  const LoginState._();
  const factory LoginState({
    Failure? failure,
    required FormzStatus status,
    required FormxState formxState,
    UserLoginType? userLoginType,
  }) = _LoginState;

  factory LoginState.initial(FormxMeta formxMeta) => LoginState(
        status: FormzStatus.pure,
        formxState: FormxState.withMeta(formxMeta),
      );

  get buttonEnabled =>
      formxState.fields[LoginFormx.fieldNames.phone]?.validAndTouched ?? false;

  get isLoading => status == FormzStatus.submissionInProgress;

  @override
  Failure? get errorState => failure;

  @override
  FormzStatus get statusState => status;

  @override
  LoginState copyWithForm(FormxState state) => copyWith(formxState: state);
}
```

> For the state management we are using Cubit with single state with the help of [freezed package](https://pub.dev/packages/freezed)
