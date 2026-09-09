# HyperionX Python Research Runtime

Private distribution of the Windows x64 Python runtime used by HyperionX Research Lab. Runtime ZIPs are release assets, not Git source files.

## Developer setup

1. Clone or update `HyperionX-Trade/HyperionX-Desktop`.
2. Download **both** `python-runtime.zip` and `python-runtime-manifest.json` from this repository's [latest release](https://github.com/HyperionX-Trade/HyperionX-Python-Runtime/releases/latest). Keep the ZIP compressed.
3. Put the two files in your Desktop clone:

   ```text
   artifacts/
     python-runtime/
       python-runtime.zip
       python-runtime-manifest.json
   ```

4. Build and run HyperionX from that clone:

   ```powershell
   dotnet build .\HyperionX\HyperionX.csproj -c Release -p:Platform=x64
   & .\HyperionX\bin\x64\Release\net8.0-windows\HyperionX.exe
   ```

5. In Research Lab, run **Check/Repair Python Engine**, then rerun the failed prescan.

HyperionX discovers the payload in `artifacts\python-runtime`, verifies its manifest hash during repair, and extracts a managed runtime under `%LOCALAPPDATA%\HyperionX\ResearchRuntime`. No global Python installation or `HYPERIONX_PYTHON` override is required. If you previously configured a developer override, remove it before using the managed runtime and restart the application.

This setup is needed once per runtime version, not after every Git pull. Download the ZIP and manifest from the same release. Existing failed jobs remain in history; rerun them after setup.

## Payload validation

Run this from the Desktop clone after downloading:

```powershell
.\Scripts\test-python-runtime-payload.ps1 `
    -PayloadDir .\artifacts\python-runtime `
    -EngineRoot .\HyperionX.ResearchEngine\python
```

This checks the SHA-256 manifest, imports the required packages, discovers strategies, and runs a sample prescan. Follow your team's PowerShell script-execution policy if unsigned repository scripts are blocked.

## Customer installers

The Desktop installer scripts stage the same two files into the installer payload. Release maintainers should run `Scripts\prepare-python-runtime.ps1` against a clean standalone Python runtime and publish the verified output here. Do not use the MSI's `-SkipPythonRuntimePayload` option for customer installers.

## Initial runtime

- Runtime ID: `python-research-win-x64-py313-20260820-full`
- Platform: Windows x64
- Required packages: pandas, numpy, matplotlib, pyarrow, yfinance, plotly
- ZIP SHA-256: `ab8bcad09cccb0e188c5eb5557cc289ea07acf54d32a386948f74127c1e24d52`
- Verified on September 9, 2026: package checks passed, 8 strategies discovered, sample prescan completed with verdict `needs_review`.

Runtime dependencies retain their upstream licenses and notices.
