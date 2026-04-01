# 12. POSIX Compatibility

## Purpose

Enable existing software:
- Vim
- Python
- GCC
- curl/wget/ssh

## Design

POSIX layer translates:

open() → broker.read_object()

## Principle

POSIX is compatibility, not architecture.

## Risk

If overused:
- system degenerates into Unix clone

## Strategy

- native first
- POSIX later
