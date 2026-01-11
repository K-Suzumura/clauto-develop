# Documentation Language Rules

This repository follows these rules for multilingual documentation.

## Languages

Japanese is the source of truth.
English documents are translations of Japanese ones.

## Structure

Japanese documents are placed under `framework-docs/ja/`.
English documents are placed under `framework-docs/en/`.
Document files under framework-docs are prefixed with a 2-digit document number in the format "00-[document-name].md".
File names MUST be identical between languages.
Japanese versions of templates and sample files outside framework-docs are named in the format [filename].ja.xxx.
Example: ./CLAUDE.ja.md.template

## File Naming

The [document-name] part of "00-[document-name].md" uses lowercase kebab-case.
Use English file names only.

## README

README.ja.md is the Japanese version (source of truth).
README.md is the English version.

Both must link to each other.

## Translation Policy

If an English translation does not exist or the content is outdated, this must be clearly stated.
