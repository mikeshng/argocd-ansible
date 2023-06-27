1. Configure the ArgoCD health check for AnsibleJob (this step won't be needed in the future after upstream contribution)

```
$ kubectl -n argocd edit cm argocd-cm
data:
  resource.customizations: |
    tower.ansible.com/AnsibleJob:
      health.lua: |
        hs = {}
        if obj.status ~= nil then
          if obj.status.ansibleJobResult ~= nil then
            if obj.status.ansibleJobResult.status == "successful" then
              hs.status = "Healthy"
              hs.message = obj.status.ansibleJobResult.url
              return hs
            end
          end
        end

        hs.status = "Progressing"
        hs.message = "Waiting for ansiblejob"
        return hs
```

1. Apply the files in `apply-me`. 

1. Modify the secret with Ansible URL and token.

1. Go to the ArgoCD UI and click `SYNC` on the `ansible-hooks` application.
