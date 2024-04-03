# TMC-SM Customizations

## GitLab_Auth_Overlays

When integrating GitLab (on premise installation) as the OIDC provider for TMC-SM, the default pinniped configuration prevents to do so.

As highlighted in this [doc](https://pinniped.dev/docs/howto/supervisor/configure-supervisor-with-gitlab/), it seems that "offline_access" scope is not working with GitLab, however it set by default in the OIDCIdentityProvider pinniped-upstream

```
    # GitLab is unusual among OIDC providers in that it returns an
    # error if you request the "offline_access" scope during an
    # authorization flow, so ask Pinniped to avoid requesting that
    # scope when using GitLab by excluding it from this list.
    # By specifying only "openid" here then Pinniped will only
    # request "openid".
```

The Overlays in this folder are removing the ```"offline_access"``` scope from the OIDCIdentityProvider at PackageInstall level after TMC installation has been done, so that Kapp controller will reconcile the required TMC packages, and thus, fix the OIDCIdentityProvider CRD to properly configure the GitLab integration for Authentication via OIDC.

To apply these overlays, the following command must be run:

```
kubectl get PackageInstall -n tmc-local tanzu-mission-control -o yaml | ytt -f tmc.yaml -f tmc-overlay.ytt -f tmc-local-stack-overlay.ytt --file - | k apply -f -
```
