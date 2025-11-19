{{/*
Nome “simples” da app
*/}}
{{- define "app-template.name" -}}
{{- if .Values.app.name -}}
{{- .Values.app.name -}}
{{- else -}}
{{- .Chart.Name -}}
{{- end -}}
{{- end }}

{{/*
Ambiente, com default pra nunca ficar nil
*/}}
{{- define "app-template.env" -}}
{{- default "dev" .Values.env -}}
{{- end }}

{{/*
Nome completo: app-ENV (ex: app-generic-dev)
*/}}
{{- define "app-template.fullname" -}}
{{- $name := include "app-template.name" . -}}
{{- $env  := include "app-template.env" . -}}
{{- printf "%s-%s" $name $env | trunc 63 | trimSuffix "-" -}}
{{- end }}

{{/*
Labels padrão SEMPRE string
*/}}
{{- define "app-template.labels" -}}
app.kubernetes.io/name: {{ include "app-template.name" . | quote }}
app.kubernetes.io/instance: {{ include "app-template.fullname" . | quote }}
app.kubernetes.io/managed-by: "harness"
app.kubernetes.io/part-of: "business-apps"
env: {{ include "app-template.env" . | quote }}
{{- end }}

----

apiVersion: v1
kind: Secret
metadata:
  name: {{ include "app-template.fullname" . }}-secret
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
type: Opaque
stringData:
  {{- range $k, $v := .Values.secretValues }}
  {{ $k }}: {{ $v | quote }}
  {{- end }}
