# Patch Types (Modify Existing Resources)**

Kustomize supports **3 main patch types**:

1. **Strategic Merge Patch (`patchesStrategicMerge`)**

   * Structured YAML merge.
   * Automatically merges lists and nested fields.
   * Example:

   ```yaml
   patchesStrategicMerge:
   - deployment-patch.yaml
   ```

2. **JSON 6902 Patch (`patchesJson6902`)**

   * Precise operations: add, replace, remove.
   * Works well for arrays or specific fields.
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
   * Example:

   ```yaml
   patches:
   - path: patch.yaml
     target:
       kind: Deployment
       name: myapp
   ```

---
