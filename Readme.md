### Pre-requisite:

- Have the operator-sdk 0.17.0 tool

- Have the OPM tool

=> Build it from here: https://github.com/operator-framework/operator-registry/blob/master/docs/design/operator-bundle.md#opm-operator-package-manager

### Useful commands

#### to create a new version of the CSV in the manifest based on what is in the deploy folder:

```bash
operator-sdk generate csv --apis-dir /home/dfestal/go/src/github.com/che-incubator/che-workspace-operator/pkg/apis --operator-name che-workspace-operator --make-manifests --csv-version=0.0.1
```
#### to create a bundle docker image from these manifests:

```bash
operator-sdk bundle create quay.io/dfestal/che-workspace-operator-catalog:0.1.1 --channels beta --package che-workspace-operator-catalog --directory deploy/olm-catalog/che-workspace-operator/manifests --overwrite --output-dir generated
docker push quay.io/dfestal/che-workspace-operator-catalog:0.1.1
```

#### to create / update and push an index that contains this bundle:

```bash
opm index add -c docker --bundles quay.io/dfestal/che-workspace-operator-catalog:0.1.1 --tag quay.io/dfestal/che-workspace-operator-index:0.1.1
docker push quay.io/dfestal/che-workspace-operator-index:0.1.1
```

#### to use this index in an OperatorHub catalog:

```bash
oc apply -f catalog-source.yaml 
```

#### Now you should see a new workspace operator available in OperatorHub and be able to use it.

### Still to look into

- Didn't experiment how to manage updates, how to add new versions in an index and have it automatically detected by OLM
- There is a problem with the registry service that is added in teh manifest. It is correctly taken in account and installed, but when the operator is removed by OLM from OperatorHub, the service is not removed, and the next OperatorHub installation will fail (=> not well supported, probably an issue to open on their side)
- Anyway you might want to remove the registry service for now if we stick to the cloud shell use-case as a first step.
- if you want the `operator-sdk generate csv` step to correctly update your CSV based on the content of teh deploy folder, you might need to rename the `o/controller.yaml` to `operator.yaml` and bring it back into the `deploy/` folder, unless you will have to provide an additinoal conf file for the `generate csv` command.