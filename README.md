# devops-paas-ansible
 Repositorio de PaaS DevOps OpenSource mediante ansible y helmchart en kubernetes

Archivos para desplegar los componentes de devops a traves de ansible desde un bastion.

Clone este repositorio

luego descarga todos los repos de ansible y de helm en la carpeta tmp/ dentro de este repo

descarga tus credenciales y asegurate que tengas un contexto de kubernetes donde vas a aprovisionar los roles

- coloca las carpetas de los roles en /tmp/roles
- coloca las carpetas de los helm charts en /tmp/deploy_helms

Edita el archivo site.yaml

Descomenta los bloques que necesites  aprovisionar y cuando estes listo lanza el playbook usando este comando

ansible-playbook site.yaml -vv

cuando todo este OK revisa que esten desplegados los charts lo puedes mirar con este comando

helm list --all-namespaces


pendiente:

- mejorar las variables de cada role ya que no toma el include_vars
- crear todos los secrets de los componentes
- poder descargar los roles tipo ansible galaxy