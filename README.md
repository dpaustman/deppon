wget http://archive.download.redhat.com/pub/redhat/linux/7.0/en/os/i386/RedHat/RPMS/rpm-build-4.0-4.i386.rpm
https://www.cnblogs.com/wongbingming/p/11086773.html
#-*- coding: utf-8 -*-
import time
from kazoo.client import KazooClient
from kazoo.recipe.watchers import ChildrenWatch

class ValidatorDetector:

    def __init__(self):
        self.zk = KazooClient(hosts='192.168.18.99:2181,192.168.18.99:2182,192.168.18.99:2183')
        self.validator_children_watcher = ChildrenWatch(client=self.zk,path='/mproxy/validators',func=self.validator_watcher_fun)
        self.zk.start()

    def validator_watcher_fun(self,children):
        print("The children now are: %s" % children)

    # def create_node(self):
    #     self.zk.create('/mproxy/validators/validator',b'validator_huabei_1',ephemeral=True,sequence=True,makepath=True)

    def __del__(self):
        self.zk.close()

    def create_node(self, path, value=b'', acl=None, ephemeral=False, sequence=False,makepath=False, include_data=False):
        self.create(path, value, ephemeral=True, sequence=True, makepath=True)

    def query(self, path, watch=None):
        node = self.get_children('%s'.format(path))
        print(node)

    def update(self, path, value, version=-1):
        self.set('%s', b'%s') % (path, value)

    def delete_node(self, path, version=-1, recursive=False):
        self.delete('path', recursive=True)



if __name__ == '__main__':
    detector = ValidatorDetector()
    detector.create_node('/data', 'nihao')
    detector.query()
    time.sleep(10)
https://blog.csdn.net/u012965373/article/details/77896512
https://blog.csdn.net/book_mmicky/article/details/39956809
https://blog.csdn.net/qq_45834006/article/details/105785139
https://blog.csdn.net/lzm1340458776/article/details/43227951 pingjun
https://blog.csdn.net/qq_41458071/article/details/105777874?
https://blog.csdn.net/hpu11/article/details/53326069

https://blog.csdn.net/u012373815/article/details/73257754 rpm打包


package test4;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.nio.charset.StandardCharsets;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Locale;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class DataProcessor {
    private static final String INPUT_FILE_PATH = "D:\\AIDATA";
    private static final String OUTPUT_FILE_PATH = "D:\\output.txt";

    private static final Pattern AIDATA_PATTERN = Pattern.compile("AIDATA\\((.*?)\\)");
    private static final Pattern UNIT_PATTERN = Pattern.compile("\\((.*?)\\)");

    public static void main(String[] args) {
        process(INPUT_FILE_PATH, OUTPUT_FILE_PATH);
    }

    private static void process(String inpputFilePath, String outputFilePath) {
        try (FileInputStream fis = new FileInputStream(inpputFilePath);
             InputStreamReader isr = new InputStreamReader(fis, "gbk");
             BufferedReader br = new BufferedReader(isr);
             FileOutputStream fos = new FileOutputStream(outputFilePath);
             OutputStreamWriter osw = new OutputStreamWriter(fos, StandardCharsets.UTF_8);
             BufferedWriter bw = new BufferedWriter(osw)) {
            String dt = null;
            String line = null;
            while ((line = br.readLine()) != null) {
                line = line.trim();
                // AIDATA开头的行，获取时间戳
                if (line.startsWith("AIDATA")) {
                    dt = RegexUtil.matchAndFindOnce(line, AIDATA_PATTERN, 1);
                }
                // 数据行
                else if (line.startsWith("(")) {
                    // 获取所有括号里的单元
                    List<String> units = RegexUtil.matchAndFindAll(line, UNIT_PATTERN, 1);
                    for (String unit : units) {
                        // 格式化并输出
                        String output = getOutput(dt, unit);
                        if (output != null) {
                            bw.write(output);
                            bw.newLine();
                        }
                    }
                }
            }
            bw.flush();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static String getOutput(String dt, String unit) {
        long ts = TimestampUtil.parse(dt);
        if (ts > 0) {
            return String.format(Locale.ROOT, "%s   %s   %s", dt, unit.replace(",", "   "), String.valueOf(ts));
        }
        return null;
    }

    /* ------------------------------ 工具 ------------------------------ */

    private static final class RegexUtil {
        private static String matchAndFindOnce(String input, Pattern pattern, int group) {
            if (input == null || input.isEmpty()) {
                return null;
            }
            Matcher matcher = pattern.matcher(input);
            if (matcher.find()) {
                return matcher.group(group);
            } else {
                return null;
            }
        }

        private static List<String> matchAndFindAll(String input, Pattern pattern, int group) {
            if (input == null || input.isEmpty()) {
                return Collections.emptyList();
            }
            List<String> list = new ArrayList<>();
            Matcher matcher = pattern.matcher(input);
            while (matcher.find()) {
                list.add(matcher.group(group));
            }
            return list;
        }

        private RegexUtil() {
        }
    }

    private static final class TimestampUtil {
        private static final String FORMAT = "yyyy-MM-dd,HH:mm:ss.SSS";

        private static final ThreadLocal<SimpleDateFormat> THREAD_LOCAL = ThreadLocal.withInitial(
                () -> new SimpleDateFormat(FORMAT)
        );

        private static long parse(String stamp) {
            try {
                return THREAD_LOCAL.get().parse(stamp).getTime();
            } catch (ParseException e) {
                e.printStackTrace();
                return -1;
            }
        }

        private TimestampUtil() {
        }
    }
}
