
# Kubernetes 的 Kustomize 设置

Kustomize 使您能够自定义 Kubernetes 的 YAML 配置文件。

## 前提条件

- 已设置 Kubernetes 集群。
- 已安装 Kustomize。

## 步骤

1. **克隆 Open WebUI 清单：**

   ```bash
   git clone https://github.com/open-webui/k8s-manifests.git
   cd k8s-manifests
   ```

2. **应用清单：**

   ```bash
   kubectl apply -k .
   ```

3. **验证安装：**

   ```bash
   kubectl get pods
   ```

## 访问 WebUI

通过设置端口转发或负载均衡，从集群外部访问 Open WebUI。
