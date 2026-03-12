# Patch Types (Modify Existing Resources)**

Kustomize supports **3 main patch types**:

## We have 3 operations in patches
* add
* replace
* delete

1. **Strategic Merge Patch (`patchesStrategicMerge`)**

   * Structured YAML merge.
   * Automatically merges lists and nested fields.
   * Will mentioon the patch file name only here and in that pacth file only will have the full manifests to be updated as patches
   * Example:

   ```yaml
   patchesStrategicMerge:
   - deployment-patch.yaml
   ```

2. **JSON 6902 Patch (`patchesJson6902`)**

   * Precise operations: add, replace, remove.
   * Works well for arrays or specific fields.
   * Will mention the target (groups,version,kind,name) along with the path where the patch file and actual pacthes will be updated in that yml file
     
   * Example:

   ```yaml
   patchesJson6902:
   - target:
       version: v1
       kind: Service
       name: myservice
     path: patch.json
   ```

3. **Unified Patches (`patches`) — modern approach**

   * Combines Strategic Merge & JSON patching.
   * Can target specific kinds or names.
   * Pacthes wil be mentioned in the same customization.yml file like inline
  
   * Example:

   ```yaml
   patches:
   - path: patch.yaml
     target:
       kind: Deployment
       name: myapp
   ```

---
