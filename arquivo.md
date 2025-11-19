{{- define "app-template.name" -}}
{{ .Values.app.name | default .Chart.Name }}
{{- end }}

{{- define "app-template.fullname" -}}
{{- printf "%s-%s" (include "app-template.name" .) .Values.env | trunc 63 | trimSuffix "-" -}}
{{- end }}

{{- define "app-template.labels" -}}
app.kubernetes.io/name: {{ include "app-template.name" . }}
app.kubernetes.io/instance: {{ include "app-template.fullname" . }}
app.kubernetes.io/managed-by: "harness"
app.kubernetes.io/part-of: "business-apps"
env: {{ .Values.env | quote }}
{{- end }}
