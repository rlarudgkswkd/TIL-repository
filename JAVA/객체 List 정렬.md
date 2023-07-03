# 객체 리스트 정렬

## Comparable을 implements 받아서 사용 가능

```
import io.swagger.annotations.ApiModel;
import lombok.Builder;
import lombok.Data;

@ApiModel(description = "Top Resource Usage")
@Data
@Builder
public class UseTop5ResourceVo implements Comparable<UseTop5ResourceVo> {
    private String instanceId;
    private String instanceName;
    private double count;

    @Override
    public int compareTo(UseTop5ResourceVo other) {
        return Double.compare(other.count, this.count);
    }
}

```

```
List<UseTop5ResourceVo> useTop5ResourceVoList = new ArrayList<>();

//후 처리
for (Ec2MetricVo ec2MetricVo : ec2MetricVoList) {
    ec2MetricVo.setCpuAvg(ec2MetricVo.getCpuAvgCount() != 0 ? ec2MetricVo.getCpuTotal() / ec2MetricVo.getCpuAvgCount() : 0);
    ec2MetricVo.setMemAvg(ec2MetricVo.getMemAvgCount() != 0 ? ec2MetricVo.getMemTotal() / ec2MetricVo.getMemAvgCount() : 0);
    ec2MetricVo.setDiskAvg(ec2MetricVo.getDiskAvgCount() != 0 ? ec2MetricVo.getDiskTotal() / ec2MetricVo.getDiskAvgCount() : 0);
    ec2MetricVo.setTotalResourceAvg((ec2MetricVo.getCpuAvg()+ec2MetricVo.getMemAvg()+ec2MetricVo.getDiskAvg())/3);

    UseTop5ResourceVo useTop5ResourceVo = UseTop5ResourceVo.builder()
            .instanceId(ec2MetricVo.getInstanceId())
            .instanceName(ec2MetricVo.getInstanceName())
            .count(calculateUtil.truncateDecimal(ec2MetricVo.getTotalResourceAvg(),2))
            .build();
    useTop5ResourceVoList.add(useTop5ResourceVo);
}

Collections.sort(useTop5ResourceVoList);

return CompletableFuture.completedFuture(useTop5ResourceVoList.subList(0,5));
```

- Collection.sort() 사용시 내림차순으로 정렬됌.
- subList 사용해서 top5 가져오기
