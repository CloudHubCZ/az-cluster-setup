dn: cn=admin,dc=ldap,dc=cloudhub,dc=cz

root@ldap:/home/cloudhub# slappasswd
New password:
Re-enter new password:
{SSHA}7yVG+5r+8TS1w5sC+kbWkYaas23b2+Xq

root@ldap:/home/cloudhub# cat basedn.ldif
dn: ou=developers,dc=cloudhub,dc=cz
objectClass: organizationalUnit
ou: developers

dn: ou=testers,dc=cloudhub,dc=cz
objectClass: organizationalUnit
ou: testers

ldapadd -x -D cn=admin,dc=ldap,dc=cloudhub,dc=cz -W -f basedn.ldif

root@ldap:/home/cloudhub# cat ldapusers.ldif
dn: uid=john,ou=developers,dc=cloudhub,dc=cz
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
uid: john
sn: johnson
userPassword: {SSHA}7yVG+5r+8TS1w5sC+kbWkYaas23b2+Xq
loginShell: /bin/bash
uidNumber: 2000
gidNumber: 2000
homeDirectory: /home/john


kubectl config set-credentials oidc \
--exec-api-version=client.authentication.k8s.io/v1beta1 \
--exec-command=kubectl \
--exec-arg=oidc-login \
--exec-arg=get-token \
--exec-arg=--oidc-issuer-url=https://auth.internal.cloudhub.cz \
--exec-arg=--oidc-client-id=oidc-auth-client \
--exec-arg=--oidc-client-secret=aGVzbG9oZXNsbw==

kubectl oidc-login setup \
--oidc-issuer-url=https://auth.internal.cloudhub.cz \
--oidc-client-id=oidc-auth-client \
--oidc-client-secret=aGVzbG9oZXNsbw==

apiVersion: v1
clusters:
- cluster:
  certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJT0tOSmNGZkNKcXN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TXpBNU1URXhOVE0yTkRWYUZ3MHpNekE1TURneE5UUXhORFZhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURMVWdWbWdwbUZ6ZmhoeWphWHJsZGRXK0d5U2RoeXJZYW9UeWUvMUdHSUdzVnphOTZCcmV6WWozS0IKaU1vWVBFUnM5Sy94VHZKL1dORWIvVUdRTlJaY2hNRzRFOE02SXlyNm1FUlZpMnpjVDBMN3VYNjRWaTluWWNqTgpYcGsxNFUrTUF6anp5T1VZcW95aEIrbmJWQ0lDNVlsOElhM2Exc2xuZ1MyalVrMWVDRlJIaFlDQm1jWWdtbUEvCkl3d0poaE5NNnBjbEF5NkxHcldUK2NoMWRuNG5kS3ZRa1VCTUxUcmhiK2xqZExVa09RVjRuaG9pODhwdzg2TDUKTVlNUHJwOGRiNm1WZFBuRTJkaG4vYUROTGFiVERNUHVoaWk3elBWM2cyaDBDMnl3RGNPN2ZScHVjZEtPQ0dodwpKYzNnM2hnRU52YnY1YkY1RFNLOFpPU2xkd2NwQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJUa2U0MU1ZcjI4ZEhrYTBUWit6ZE80ZjBTTkFUQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ0ZETEw2RnBHbQpZSVRyYXdyZmsvdnhYUU5sUVZzWDhxOG5HYnBTMThYdVJqeVErMk54dHYrYlRrWURnWW1yR2I4QzRWWW5vaDFLCnVVbkFkcXBOM3VPTkR4NlBONzlHZ0hMdlJLaWM2SjMwNFp4ekxCeDRGZjNYSzNhU3FOaDIvMlF2azcva3dXMmcKc1d4Y3diRWNweHJ0Z3YvYjJQR0Flc3VYRVR6Wk5UaVBOcXFjeFREbHQ0OER5VDhPa0hRUWpXNHdFdm16U2NDawpncUxvTFNWQjc2SHd4c0YxQkhsNVc1bTJMWXdrWkFyUkZtN0ptd2JPaTZZbG81bVhGY3VHR2wveUR3RnNQT0RoCkRUVjFSSm14V2NQUGtXd2FIWW1YYWNDU0FpZkZ6cVIxSWlON01QUVoxZk1HM3ZVWFA4N2lFUjZBQ28vWVBDS2kKVS8weEllNUp0ckoxCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
  server: https://52.174.80.151:6443
  name: cloudhub-cluster
  contexts:
- context:
  cluster: cloudhub-cluster
  name: kubernetes-admin@cloudhub-cluster
  current-context: kubernetes-admin@cloudhub-cluster
  kind: Config
  preferences: {}
  users:
- name: oidc
  user:
  exec:
  apiVersion: client.authentication.k8s.io/v1beta1
  args:
  - oidc-login
  - get-token
  - --oidc-issuer-url=https://auth.internal.cloudhub.cz
  - --oidc-client-id=oidc-auth-client
  - --oidc-client-secret=aGVzbG9oZXNsbw==
  command: kubectl
  env: null
  provideClusterInfo: false
