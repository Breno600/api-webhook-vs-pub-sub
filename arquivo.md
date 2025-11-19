apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "app-template.fullname" . }}
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "app-template.name" . }}
      env: {{ .Values.env }}
  template:
    metadata:
      labels:
        {{- include "app-template.labels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ include "app-template.name" . }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - containerPort: {{ .Values.service.targetPort }}
          env:
            # NÃO sensíveis
            {{- range $k, $v := .Values.config }}
            - name: {{ $k }}
              value: {{ $v | quote }}
            {{- end }}
            # sensíveis → via Secret
            {{- range .Values.secretKeys }}
            - name: {{ . }}
              valueFrom:
                secretKeyRef:
                  name: {{ include "app-template.fullname" $ }}-secret
                  key: {{ . }}
            {{- end }}

---

apiVersion: v1
kind: Service
metadata:
  name: {{ include "app-template.fullname" . }}
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  selector:
    app.kubernetes.io/name: {{ include "app-template.name" . }}
    env: {{ .Values.env }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
      protocol: TCP
      name: http
---

apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "app-template.fullname" . }}-config
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
data:
  {{- range $k, $v := .Values.config }}
  {{ $k }}: {{ $v | quote }}
  {{- end }}
---
apiVersion: v1
kind: Secret
metadata:
  name: {{ include "app-template.fullname" . }}-secret
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
type: Opaque
stringData:
  {{- range .Values.secretKeys }}
  {{ . }}: ""   # Harness/CI/Secret Manager vai sobrescrever isso em runtime
  {{- end }}
---

app:
  name: "app-a"
  namespace: "apps-dev"

env: "dev"

image:
  repository: "registry.example.com/app-a"
  tag: "1.0.0-dev"

replicaCount: 1

config:
  LOG_LEVEL: "debug"
  FEATURE_X_ENABLED: "true"
  TIMEOUT: "3s"

secretKeys:
  - DB_HOST
  - DB_USER
  - DB_PASSWORD
  - API_KEY

---

{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ include "app-template.fullname" . }}
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
  {{- with .Values.ingress.annotations }}
  annotations:
    {{- toYaml . | nindent 4 }}
  {{- end }}
spec:
  {{- if .Values.ingress.className }}
  ingressClassName: {{ .Values.ingress.className }}
  {{- end }}
  {{- if .Values.ingress.tls }}
  tls:
    {{- range .Values.ingress.tls }}
    - secretName: {{ .secretName }}
      hosts:
        {{- range .hosts }}
        - {{ . | quote }}
        {{- end }}
    {{- end }}
  {{- end }}
  rules:
    {{- range .Values.ingress.hosts }}
    - host: {{ .host | quote }}
      http:
        paths:
          {{- range .paths }}
          - path: {{ .path }}
            pathType: {{ .pathType | default "Prefix" }}
            backend:
              service:
                name: {{ include "app-template.fullname" $ }}
                port:
                  number: {{ .backendPort | default $.Values.service.port }}
          {{- end }}
    {{- end }}
{{- end }}
