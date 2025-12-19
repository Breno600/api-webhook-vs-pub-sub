{{- if .Values.externalSecrets.enabled }}
{{- $storeName := .Values.externalSecrets.storeRef.name }}
{{- $storeKind := .Values.externalSecrets.storeRef.kind | default "SecretStore" }}

{{- range $es := .Values.externalSecrets.secrets }}
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: {{ include "java-chart.fullname" $ }}-{{ $es.name }}
  labels:
    {{- include "java-chart.labels" $ | nindent 4 }}
spec:
  refreshInterval: {{ $es.refreshInterval | default "1h" }}
  secretStoreRef:
    name: {{ $storeName }}
    kind: {{ $storeKind }}
  target:
    name: {{ $es.target.name }}
    creationPolicy: Owner
    template:
      type: Opaque
  data:
    {{- range $es.data }}
    - secretKey: {{ .secretKey }}
      remoteRef:
        key: {{ .remoteRef.key | quote }}
        {{- if .remoteRef.property }}
        property: {{ .remoteRef.property | quote }}
        {{- end }}
        {{- if .remoteRef.version }}
        version: {{ .remoteRef.version | quote }}
        {{- end }}
        {{- if .remoteRef.decodingStrategy }}
        decodingStrategy: {{ .remoteRef.decodingStrategy }}
        {{- end }}
    {{- end }}
{{- end }}
{{- end }}

# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "java-chart.fullname" . }}
  labels:
    {{- include "java-chart.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount | default 1 }}
  {{- end }}
  selector:
    matchLabels:
      {{- include "java-chart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      labels:
        {{- include "java-chart.selectorLabels" . | nindent 8 }}
        env: {{ .Values.env | quote }}
        {{- with .Values.podLabels }}
        {{- toYaml . | nindent 8 }}
        {{- end }}
    spec:
      {{- with .Values.imagePullSecrets }}
      imagePullSecrets:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      serviceAccountName: {{ include "java-chart.serviceAccountName" . }}

      {{- with .Values.podSecurityContext }}
      securityContext:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      containers:
        - name: {{ .Chart.Name }}

          {{- with .Values.securityContext }}
          securityContext:
            {{- toYaml . | nindent 12 }}
          {{- end }}

          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy | default "IfNotPresent" }}

          env:
            {{- range $k, $v := .Values.config }}
            - name: {{ $k }}
              value: {{ $v | quote }}
            {{- end }}

            {{- range .Values.secretEnv }}
            - name: {{ .name }}
              valueFrom:
                secretKeyRef:
                  name: {{ .secretName | quote }}
                  key: {{ .secretKey | quote }}
                  {{- if hasKey . "optional" }}
                  optional: {{ .optional }}
                  {{- end }}
            {{- end }}

          {{- with .Values.resources }}
          resources:
            {{- toYaml . | nindent 12 }}
          {{- end }}

          {{- with .Values.volumeMounts }}
          volumeMounts:
            {{- toYaml . | nindent 12 }}
          {{- end }}

      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: app
                    operator: In
                    values:
                      - {{ .Chart.Name }}
              topologyKey: "kubernetes.io/hostname"

      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: "topology.kubernetes.io/zone"
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: {{ .Chart.Name }}
        - maxSkew: 1
          topologyKey: "kubernetes.io/hostname"
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: {{ .Chart.Name }}

      {{- with .Values.volumes }}
      volumes:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.affinity }}
      affinity:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
