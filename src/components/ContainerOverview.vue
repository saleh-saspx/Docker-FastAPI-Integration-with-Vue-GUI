<template>
  <div>
    <table>
      <thead>
      <tr>
        <td style="text-align: left !important;"><strong>CPU Stats:</strong></td>
        <td style="text-align: left !important;"><strong>Memory Stats:</strong></td>
        <td style="text-align: left !important;"><strong>Throttling Data:</strong></td>
      </tr>
      </thead>
      <tbody>
      <tr>
        <td style="text-align: left !important;">
          <p>Total Usage: <strong>{{ this.convertBytes(data.cpu_stats.cpu_usage.total_usage) }}</strong></p>
          <p>Usage in Kernel Mode: <strong>{{ this.convertBytes(data.cpu_stats.cpu_usage.usage_in_kernelmode) }}</strong></p>
          <p>Usage in User Mode: <strong>{{ this.convertBytes(data.cpu_stats.cpu_usage.usage_in_usermode) }}</strong></p>
          <p>System CPU Usage: <strong>{{ this.convertBytes(data.cpu_stats.system_cpu_usage) }}</strong></p>
          <p>Online CPUs: <strong>{{ data.cpu_stats.online_cpus }}</strong></p>
        </td>
        <td style="text-align: left !important;">
          <p>Usage: <strong>{{ this.convertBytes(data.memory_stats.usage) }}</strong></p>
          <p>Limit: <strong>{{ this.convertBytes(data.memory_stats.limit) }}</strong></p>
        </td>
        <td style="text-align: left !important;">
          <p>Periods: <strong>{{ data.cpu_stats.throttling_data.periods }}</strong></p>
          <p>Throttled Periods: <strong>{{ data.cpu_stats.throttling_data.throttled_periods }}</strong></p>
          <p>Throttled Time: <strong>{{ data.cpu_stats.throttling_data.throttled_time }}</strong></p>
        </td>
      </tr>

      </tbody>
    </table>
  </div>
</template>

<script>

export default {
  props: {
    data: Object,
  },
  methods: {
    convertBytes(bytes = 0) {
      const units = ['Bytes', 'KB', 'MB', 'GB', 'TB'];
      let index = 0;
      while (bytes >= 1024 && index < units.length - 1) {
        bytes /= 1024;
        index++;
      }
      return bytes.toFixed(2) + ' ' + units[index];
    }
  }
}
</script>
