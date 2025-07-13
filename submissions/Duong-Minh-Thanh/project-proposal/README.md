# Multi-Architecture Deployment với ARM và x86 Instances

## Xây dựng CI/CD Pipeline hỗ trợ triển khai đa kiến trúc trên AWS

---

# Executive Summary

Đề tài này nhằm giải quyết bài toán tối ưu hoá triển khai ứng dụng trên nhiều kiến trúc phần cứng khác nhau (ARM Graviton và x86) trong môi trường AWS, thông qua việc phát triển hệ thống CI/CD pipeline hỗ trợ multi-architecture deployment. Việc tận dụng lợi thế của kiến trúc ARM (hiệu suất cao, chi phí thấp, tiết kiệm năng lượng) so với x86 giúp doanh nghiệp tối ưu hoá chi phí vận hành mà vẫn đảm bảo hiệu năng và độ tương thích ứng dụng.

Giải pháp đề xuất bao gồm:

* Xây dựng pipeline CI/CD hỗ trợ build và test ứng dụng trên cả hai nền tảng.
* Áp dụng kỹ thuật cross-compilation và sử dụng multi-arch Docker images.
* Thiết lập môi trường kiểm thử tự động với hệ thống benchmark hiệu năng, tiêu thụ năng lượng và chi phí.

Lợi ích dự án bao gồm:

* Tiết kiệm đến 30% chi phí compute nhờ tối ưu deployment lên ARM Graviton.
* Tăng khả năng tương thích và khả năng mở rộng đa nền tảng.
* Tạo tiền đề cho các chiến lược hiện đại hóa hệ thống.

Chi phí đầu tư chủ yếu bao gồm chi phí phát triển pipeline, triển khai thử nghiệm, chi phí EC2 (Graviton và x86), và nhân sự triển khai. Thời gian thực hiện dự kiến **8 tuần**.

Kết quả kỳ vọng gồm: bộ công cụ CI/CD hoàn chỉnh, báo cáo đánh giá hiệu suất và chi phí, tài liệu hướng dẫn triển khai thực tế.

---

# 1. Problem Statement

## Current Situation

Nhiều doanh nghiệp sử dụng hạ tầng AWS vẫn đang chạy ứng dụng chủ yếu trên x86 mà chưa khai thác hiệu quả kiến trúc ARM Graviton. Điều này dẫn đến chi phí cao và chưa tối ưu về hiệu năng năng lượng.

## Key Challenges

* Thiếu CI/CD pipeline hỗ trợ song song cả ARM và x86.
* Thiếu chiến lược migration và testing đa kiến trúc.
* Chưa có hệ thống benchmark để so sánh hiệu năng và chi phí giữa hai nền tảng.

## Stakeholder Impact

* DevOps team gặp khó khăn khi build/test trên nhiều nền tảng.
* Bộ phận tài chính khó kiểm soát chi phí tối ưu.
* Sản phẩm chịu rủi ro nếu thiếu kiểm thử compatibility.

## Business Consequences

Không tận dụng được ARM Graviton khiến doanh nghiệp mất cơ hội tiết kiệm chi phí (tới 40%) và nâng cao hiệu năng. Ngoài ra, việc không kiểm thử đa kiến trúc làm giảm tính bền vững và mở rộng của hệ thống.

---

# 2. Solution Architecture

## Architecture Overview

CI/CD pipeline được thiết kế để build, test và deploy trên cả hai kiến trúc ARM và x86 thông qua Docker multi-arch images và cross-compilation workflows.

## AWS Services Used

* AWS CodePipeline, CodeBuild: orchestrate và build ứng dụng đa kiến trúc
* EC2 Graviton & x86: môi trường kiểm thử
* ECR: lưu trữ image
* CloudWatch & CloudTrail: giám sát hiệu năng và logs

## Component Design

1. Git repository → Webhook trigger → CodePipeline
2. CodeBuild chạy build đa kiến trúc → push image multi-arch lên ECR
3. Deploy thử nghiệm lên hai nhóm EC2 (ARM & x86)
4. CloudWatch thu thập metrics: thời gian, CPU, RAM, chi phí

## Security Architecture

* IAM roles hạn chế quyền theo principle of least privilege
* Sử dụng encryption cho ECR, logging và artifact

## Scalability Design

* Build matrix cho phép scale các job build đa nền tảng song song
* Tự động mở rộng EC2 nhóm benchmark khi có workload lớn

---

# 3. Technical Implementation

## Implementation Phases

1. POC Pipeline đơn giản (1 tuần)
2. Tích hợp cross-compilation & multi-arch Docker build (2 tuần)
3. Thiết lập môi trường kiểm thử ARM và x86 (1 tuần)
4. Benchmark performance và chi phí (2 tuần)
5. Tài liệu hoá và optimize (1 tuần)
6. Final review và trình bày kết quả (1 tuần)

## Technical Requirements

* Docker with Buildx, QEMU
* AWS CLI, CodeBuild spec
* EC2 t4g.medium và t3.medium

## Development Approach

* IaC bằng CloudFormation hoặc Terraform
* Sử dụng Github Actions để phối hợp với CodeBuild

## Testing Strategy

* So sánh startup time, CPU%, memory%, cost/hour
* Chạy test cases và stress benchmark song song

## Deployment Plan

* Push lên ECR theo tag: arch-x86, arch-arm
* Canary deploy từng loại lên các EC2 tương ứng
* Rollback nếu có incompatibility hoặc lỗi hiệu năng

---

# 4. Timeline & Milestones

## Project Timeline

Tổng thời gian: **8 tuần**

## Key Milestones

* Tuần 1: POC pipeline hoạt động
* Tuần 2–3: Build đa kiến trúc thành công, image push lên ECR
* Tuần 4–5: Benchmark performance & cost
* Tuần 6: Optimize CI/CD và soạn thảo guideline
* Tuần 7: Tổng hợp tài liệu, kiểm tra final
* Tuần 8: Final report và trình bày

## Dependencies

* AWS Account với quyền EC2, CodeBuild, ECR
* Docker environment hỗ trợ buildx

## Resource Allocation

* 1 DevOps (full-time)
* 1 Developer (part-time)
* 1 Reviewer (PM)

---

# 5. Budget Estimation

## Infrastructure Costs

* EC2: t4g.medium và t3.medium (\~100–150 giờ thử nghiệm)
* ECR: lưu trữ multi-arch images (\~5–10 USD)
* CodeBuild: runtime (\~20 USD/tháng)

## Development Costs

* Nhân sự DevOps: 8 tuần (\~1,600–2,000 USD nếu tính freelance)

## Operational Costs

* Monitoring CloudWatch (\~3–5 USD)

## ROI Analysis

* Tiết kiệm compute lên tới 30–40% khi migrate x86 → ARM
* Pipeline có thể tái sử dụng cho nhiều project

---

# 6. Risk Assessment

## Risk Matrix

| Rủi ro                                 | Tác động   | Xác suất   | Độ ưu tiên |
| -------------------------------------- | ---------- | ---------- | ---------- |
| Incompatibility của ứng dụng với ARM   | Cao        | Trung bình | Cao        |
| Thiếu kinh nghiệm về cross-compilation | Trung bình | Cao        | Cao        |
| Chi phí vượt dự đoán                   | Thấp       | Thấp       | Thấp       |

## Mitigation Strategies

* Chọn app đơn giản làm thử nghiệm trước
* Sử dụng mẫu từ AWS Docs về multi-arch CI/CD
* Giới hạn thời gian benchmark

## Contingency Plans

* Nếu ARM fail → giữ x86 pipeline sẵn để rollback
* Nếu CI/CD build fail → build thủ công tạm thời

---

# 7. Expected Outcomes

## Success Metrics

* CI/CD pipeline build được image ARM và x86 thành công
* So sánh benchmark rõ ràng về cost/performance
* Tài liệu hướng dẫn triển khai cho team

## Business Benefits

* Giảm chi phí compute từ 20–40%
* Tăng khả năng mở rộng ứng dụng

## Technical Improvements

* Hỗ trợ cross-platform build
* Tự động kiểm thử và benchmark đa kiến trúc

## Long-term Value

* Có thể mở rộng pipeline cho các microservice
* Tối ưu hoá chi phí lâu dài cho toàn bộ hệ thống

---

# Appendices

## A. Technical Specifications

* Docker Buildx + QEMU
* EC2 instance types: t4g.medium, t3.medium

## B. Cost Calculations

Chi tiết chi phí EC2, ECR, CodeBuild được tính bằng AWS Pricing Calculator

## C. Architecture Diagrams

Sơ đồ CI/CD pipeline đa kiến trúc với flow từ Git → CodeBuild → ECR → EC2

## D. References

* [AWS Graviton documentation](https://aws.amazon.com/ec2/graviton/)
* [AWS CodeBuild - Multi-arch Docker builds](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-docker.html#sample-docker-multiarch)
* [AWS Pricing Calculator](https://calculator.aws.amazon.com)
