# Direct verification — later phase

This document records the completed direct source-inspection phase, separately from the original REA findings. No app was run or modified. No analysis or comparisons were rerun to save this report.

Archive: C:\Users\harsh\rea-demo\dist\app.asar
Staging: C:\Users\harsh\rea-demo\build\app
Original: C:\Users\harsh\rea-demo

## Method

PowerShell Get-FileHash calculated archive SHA-256. A Node inspection script loaded only the installed @electron/asar reader and Node fs/path/crypto modules, not app modules. It used listPackage, statFile, and extractFile to read members in memory, calculated hashes, and compared bytes with Buffer.equals. No files were extracted to disk. The initial report was retrieved from the chat Investigate REA note persistence for comparison.

All source locations below are ASAR member locations; the staged and original copies have identical bytes and line locations.

## Findings

1. **Search — confirmed.** renderer.js:14: `const query = search.value.toLowerCase();` renderer.js:15: `const visible = notes.filter(note => note.text.toLowerCase().includes(query));` This confirms lowercase-normalized substring matching, no query trimming, and empty-query matching from the REA reconstruction.

2. **Storage — confirmed; initial uncertainty resolved only in this later phase.** main.js:85: `notesFile = path.join(app.getPath('userData'), 'notes.json');`. The directory selector is exactly userData and filename exactly notes.json. The actual absolute runtime directory was not measured. Initial uncertainties are retained unchanged in rea-investigation.md.

3. **Deletion — confirmed; initial operator gap resolved only in this later phase.** main.js:56: `return save(notes.filter(note => note.id !== id));`. Strict inequality retains IDs different from the requested ID and passes that array to save. The earlier supported deletion-to-save mechanism is consistent with these bytes.

4. **Archive versus staging and original — confirmed.** Exact inventory equality with build/app; six members, all packed (unpacked: false). Each matched its staged and original counterpart byte-for-byte:

| Member | Bytes | SHA-256 | Staged match | Original match |
|---|---:|---|---|---|
| index.html | 1069 | 4b1ae0467d026dad35c1d064807347ce93d2120b8b61c546e42541a0daddafc1 | Yes | Yes |
| main.js | 3604 | 6332ddeaecaf2d9011731309090477c942d5cc9a051e6c71afec27e1655c3228 | Yes | Yes |
| package.json | 428 | c1890ba3c69cdfd93785714a196991b47f99b14ae601d626a321ae21c456358c | Yes | Yes |
| preload.js | 346 | e2ae7b7cf7ecd0b26e88b6c134ff3b1fd7f372e78a9d6d3a5fdccc2bebe6e12d | Yes | Yes |
| renderer.js | 2318 | 898157fd06f43e9e6d48b4d76f70e8c49ae6cc27ed6bc5519e38fe3dd74fbecc | Yes | Yes |
| styles.css | 1058 | 8ab7484610885f7c1de2cb0270e040c03f225585ba8e6ac0260631a71e7356f8 | Yes | Yes |

5. **Archive hash versus initial REA report — confirmed.** Both PowerShell and Node independently returned the same SHA-256 as the earlier report:

```text
16ca3365959cbf4b89d858abe52a473e5c1e91f415e64cc0c9cc4dc8ecc4f492
```

## Limits and preservation

No earlier claim was contradicted. Runtime dispatch, successful runtime persistence, and crash durability remain unverified. This direct inspection does not retroactively change what REA could establish in the initial phase. These results describe the files at verification time.

This report preserves the observed expressions, byte comparisons, member sizes and hashes from the completed verification outputs. It is not a raw terminal transcript or a copy of the source/archive. No separate machine-readable comparison artifact was saved during verification. Raw REA responses and the full retained evidence bundle are not included; see the initial report's preservation limits.
