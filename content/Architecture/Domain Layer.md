The domain layer houses the business contracts for the module, encompassing repositories, entities, and use cases.

## Entity

Entity denotes a data class utilized across domain and presentation layers. If an Entity is used as a parameter, it should be suffixed with Param (e.g., LoginParam). Entities are created using Freeze, with all fields being required if possible.

^9b83c7

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'mall.freezed.dart';

@freezed
class Mall with _$Mall {
  const factory Mall({
    required String id,
    required String directoryCode,
    required String directoryName,
    required String logo,
    required String createdAt,
    required String updatedAt,
  }) = _Mall;
}
```

## Repository

Repositories possess distinct threads where two files are dedicated to them: one for the interface and one for the implementation. The domain layer houses the interface, solely consisting of the business contract of the repository. Its implementation is in the Data layer.

```dart
import 'package:common_dependency/common_dependency.dart';

abstract interface class AuthRepository {
  Future<Either<Failure, bool>> validatePhoneNumber(String phoneNumber);
  Future<Either<Failure, Unit>> forgotPin(String phoneNumber);
  Future<Either<Failure, Unit>> register(RegisterParam param);
  Future<Either<Failure, Unit>> validateOtp(String token);
  Either<Failure, AuthRuntimeData> getRuntimeData();
  Either<Failure, Unit> setRunTimeData(AuthRuntimeData newData);
  Future<Either<Failure, Unit>> login(LoginParam param);
  Future<Either<Failure, Unit>> submitNewPin(
      PinPageType pinPageType, String pin);
  Future<Either<Failure, List<Taste>>> getPreference();
  Future<Either<Failure, UserAuth>> setPreference(List<String> prefIds);
  Either<Failure, UserAuth> checkLoggedIn();
  Future<Either<Failure, Unit>> logout();
  Either<Failure, UserAuth> getLocalUserAuth();
  Future<Either<Failure, UserAuth>> getMemberProfile();
  Future<Either<Failure, Unit>> validateLink(String memberId);
  Future<Either<Failure, BiometricResponse>> getBiometric();
  Either<Failure, Unit> setBiometric(bool biometric);
  Either<Failure, Unit> setPin(String pin, bool update);
}
```

> Note that our Repository follows a functional programming paradigm for its return type. It will either return a Fail object or the desired result object. We leverage the [dartz package](https://pub.dev/packages/dartz) to implement this functionality.

## UseCase

UseCase represents specific functionalities or discrete actions that users or systems can perform within the application. It acts as a bridge between state management (Presentation layer) and Repository (Data layer). In cases where API integration is pending, mocking or bypassing data can occur within this layer.

```dart
import 'package:auth/src/presentation/pages/register/cubit/form/register_form.dart';
import 'package:common_dependency/common_dependency.dart';

class RegisterUseCase extends BaseFormUseCase {
  RegisterUseCase(this._repository);
  final AuthRepository _repository;

  @override
  Future<Either<Failure, dynamic>> call(Map<String, dynamic> data,
      {Map<String, dynamic>? option}) async {
    const fieldNames = RegisterFormx.fieldNames;
    return await _repository.register(
      RegisterParam(
        email: data[fieldNames.email],
        fullname: data[fieldNames.fullName],
      ),
    );
  }
}
```

> The UseCase acts as the intermediary linking the presentation layer with the domain layer, facilitating communication between the two. Cubits invoke the UseCase to initiate actions and manage state within the presentation layer.
