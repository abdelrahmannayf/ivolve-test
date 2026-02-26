# 🚀 End-to-End DevOps Automation Lab

مشروع متكامل يجمع بين أتمتة البنية التحتية (Ansible) وبناء خطوط الإنتاج (Jenkins) والنشر المستمر (ArgoCD).

---

## 🛠️ Infrastructure as Code (Ansible Roles)
استخدمنا **Ansible Roles** لتنظيم السيرفرات وتثبيت الأدوات بشكل ذكي (Idempotent).

### 🔹 الأدوار المستخدمة (Roles):
* **Docker:** تثبيت المحرك وضمان استقراره.
* **Kubectl:** تهيئة بيئة التحكم في Kubernetes.
* **Jenkins:** تثبيت وإعداد محرك الـ CI.

```bash
# تشغيل الـ Playbook
ansible-playbook -i inventory.ini site.yml --ask-become-pass
