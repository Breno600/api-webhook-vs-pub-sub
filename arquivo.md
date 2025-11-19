{{/*
  Service opcional + defaults seguros
*/}}

{{- /* controla se o Service será criado ou não */ -}}
{{- $svcEnabled := true -}}
{{- if .Values.service }}
  {{- if hasKey .Values.service "enabled" }}
    {{- $svcEnabled = .Values.service.enabled -}}
  {{- end }}
{{- end }}

{{- if $svcEnabled }}

  {{- /* defaults */ -}}
  {{- $svcType := "ClusterIP" -}}
  {{- $svcPort := 80 -}}
  {{- $targetPort := $svcPort -}}

  {{- if .Values.service }}
    {{- if .Values.service.type }}
      {{- $svcType = .Values.service.type -}}
    {{- end }}
    {{- if .Values.service.port }}
      {{- $svcPort = .Values.service.port -}}
      {{- $targetPort = .Values.service.port -}}
    {{- end }}
    {{- if .Values.service.targetPort }}
      {{- $targetPort = .Values.service.targetPort -}}
    {{- end }}
  {{- end }}

apiVersion: v1
kind: Service
metadata:
  name: {{ include "app-template.fullname" . }}
  labels:
    {{- include "app-template.labels" . | nindent 4 }}
spec:
  type: {{ $svcType }}
  ports:
    - name: http
      port: {{ $svcPort }}
      targetPort: {{ $targetPort }}
      protocol: TCP
  selector:
    app.kubernetes.io/name: {{ include "app-template.name" . }}
    app.kubernetes.io/instance: {{ include "app-template.fullname" . }}
    env: {{ include "app-template.env" . }}

{{- end }}
