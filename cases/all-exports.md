# All exports

Conversion of a platform into all available formats.

See details in [docs](https://hetalang.github.io/#/heta-compiler/export-formats).

```heta
...
// Heta-based formats

#export { format: JSON, filepath: json, noUnitsExpr: false, spaceFilter: [nameless] }; // convert only global and components of "nameless"

#export { format: YAML, filepath: yaml, noUnitsExpr: false };

#export { format: XLSX, filepath: table, omitRows: 3, splitByClass: true };

#export { format: XLSX, filepath: xlsx, omitRows: 3, splitByClass: true };

#export { format: HetaCode, filepath: heta-code, noUnitsExpr: false };

// process-description formats

#export { format: SBML, filepath: sbml, version: L2V4 };

#export { format: Simbio, filepath: simbio };

// ODE-based formats

#export { format: SLV, filepath: slv, eventsOff: false, powTransform: function, groupConstBy: tags[0] };

#export { format: DBSolve, filepath: dbsolve, powTransform: function, groupConstBy: tags[0] };

#export { format: Mrgsolve, filepath: mrgsolve };

#export { format: Matlab, filepath: matlab };

#export { format: Julia, filepath: julia };

```
