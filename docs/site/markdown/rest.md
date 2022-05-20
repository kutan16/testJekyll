# REST API Specification Overview

## Overview

This document provides an overview of the External Application Trust Service API. The following characteristics apply to all API resources:

## Content Type

Only JSON format is supported. All requests must specify `application/json` in their `Accept` header. Responses will
have a `Content-Type` of `application/json;charset=UTF-8`.

## Resources

| Resource Method                             | Description                          |
|---------------------------------------------|--------------------------------------|
| [`POST` /trust-info/applications](./get-trust-info.html) | Trust Info for applications |
