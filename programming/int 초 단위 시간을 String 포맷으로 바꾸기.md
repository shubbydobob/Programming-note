* 상황 *

- 쿼리문에서 누적 시간, 시간을 초 단위로 보내준다.
- VO는 INT로 받기 때문에 00:00:00 같은 문자열로 받을 수 없을 때,
  String 포맷으로 변경해서 사용.

* 로직 * 

private String formatSecondsToTime(int totalSeconds) {
        int hours = totalSeconds / 3600;
        int minutes = (totalSeconds % 3600) / 60;
        int seconds = totalSeconds % 60;
        return String.format("%02d:%02d:%02d", hours, minutes, seconds);
    }

  ex) int time;
      
      int time =vo.getTime()
      map.put("drivingTime", formatSecondsToTime(drivingTime));
