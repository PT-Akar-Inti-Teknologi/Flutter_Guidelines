---
title: 
aliases: []
---
The Data Layer is responsible for all communication with external data sources, such as fetching data from APIs or accessing device cache. It consists of three main components: Models, Repositories, and DataSources.

## Models (DTO)

Models, or Data Transfer Objects (DTOs), represent the data structures used within the data layer. These models encapsulate data that is passed around within the data layer, separating it from the internal implementation details of the application.
To promote loose coupling and separation of concerns, models are named with suffixes like "Response" or "Request" depending on their usage (e.g., LoginResponse, LoginRequest).

^321fec

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'mall_response.freezed.dart';
part 'mall_response.g.dart';

@freezed
class MallResponse with _$MallResponse {
  const factory MallResponse({
    String? id,
    @JsonKey(name: 'directory_code') String? directoryCode,
    @JsonKey(name: 'directory_name') String? directoryName,
    String? logo,
    @JsonKey(name: 'created_at') String? createdAt,
    @JsonKey(name: 'updated_at') String? updatedAt,
  }) = _MallResponse;

  factory MallResponse.fromJson(Map<String, dynamic> json) =>
      _$MallResponseFromJson(json);
}
```

## Mappers

Mappers convert entities into models and vice versa. Since we have entities for different layers, mappers facilitate the conversion between these layers. This results in a structure where each data class consists of three files :
[[Domain Layer#^9b83c7|Entity]], [[Data Layer#^321fec|Model]], and [[Data Layer#^509b52|Mapper]].

^509b52

```dart
import '../../models/mall/mall_response.dart';
import '../../../domain/entities/mall/mall.dart';

extension MallResponseMapper on MallResponse {
  Mall get toEntity {
    return Mall(
      id: id ?? '',
      directoryCode: directoryCode ?? '',
      directoryName: directoryName ?? '',
      logo: logo ?? '',
      createdAt: createdAt ?? '',
      updatedAt: updatedAt ?? '',
    );
  }
}
```

## Repositories

Repositories encapsulate the abstraction of data access defined in the domain layer. Here, where we store the implementation of repositories.

```dart
class AuthRepositoriesImpl implements AuthRepository {
  final AuthRemoteDataSource _remote;
  final AuthLocalDataSource _local;
  final FirebaseModule _firebase;
  AuthRuntimeData _runtimeData = const AuthRuntimeData();

  AuthRepositoriesImpl(this._remote, this._local, this._firebase);

  @override
  Future<Either<Failure, bool>> validatePhoneNumber(String phoneNumber) async {
    return apiTryCatch(
      execute: () async {
        if (phoneNumber.startsWith('0')) {
          phoneNumber = '+62${phoneNumber.substring(1)}';
        }
        _runtimeData = _runtimeData.copyWith(userPhoneNumber: phoneNumber);
        final res = await _remote.validatePhoneNumber(phoneNumber);
        final data = res.toEntity;
        if (data.isVerified) return const Right(true);
        _runtimeData =
            _runtimeData.copyWith(userSecretId: data.secretId, id: data.id);
        return const Right(false);
      },
      additionalErrorCondition: (e) async {
        if (e.checkFromSchemaCode(
          SchemaErrorCodes.validatePhoneUnregisteredUser,
        )) {
          return const Left(UnregisteredUserFailure());
        }
        return Left(ErrMsgFailure(e.errorMsg));
      },
    );
  }
}
```

> Note that our Repository follows a functional programming paradigm for its return type. It will either return a Fail object or the desired result object. We leverage the [dartz package](https://pub.dev/packages/dartz) to implement this functionality.

## DataSources

DataSources are where we interact with external data sources, such as API services or device storage. Each source of data has its own file to ensure separation of concerns. For example, API services and data fetching from storage should not be combined into a single file or class.

```dart
class AuthRemoteDataSourceImpl implements AuthRemoteDataSource {
  final DioService service;

  AuthRemoteDataSourceImpl(this.service);

  @override
  Future<ValidatePhoneResponse> validatePhoneNumber(String phoneNumber) async {
    return await service.post(
      endpoint: AuthEndpoint.validatePhoneNumber,
      data: {"phone_number": phoneNumber},
      converter: (response) {
        return ValidatePhoneResponse.fromJson(response.responseOutputDetail);
      },
    );
  }
}
```
