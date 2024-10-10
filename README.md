<div align="center">
  <img src="https://github.com/user-attachments/assets/6c7cb64b-4c2c-484c-822f-ecf840de1bb5">  <br><br>
  <p>기업으로부터 수주를 받아 진행된 정부 지원 프로젝트입니다.</p>
  <p>인천 광역시의 문화재를 알리기 위한 취지로, 커뮤니티 형식의 Prototype으로 제작되었습니다.</p> <br><br>
</div>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>개발 기간 :</strong> 2024.04.01 ~ 2024.05.08<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>개발 인원 :</strong> 4명<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>담당 역할 :</strong> Restful API를 기반으로 CRUD 구현, JPA를 이용한 페이징과 검색 기능 구현<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>언어 :</strong> Java (JDK 17.0.9), HTML/CSS, JavaScript<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>서버 :</strong> Apache Tomcat 9.0<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>프레임워크 :</strong> Spring Boot 3.2.4<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>DB :</strong> PostgreSQL<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>IDE :</strong> Spring Tool Suite 4.21.1, Postman, HeidiSQL, Visual Studio Code<br><br>
&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
<strong>API, 라이브러리 :</strong> Restful API (JSON), JPA, React<br><br><br><br><br><br><br><br>

[ CODE ]<br><br>

● Config Package<br><br>

1. WebMvcConfigurer.java
~~~java
package com.green.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;

@Configuration
public class WebMvcConfigurer implements org.springframework.web.servlet.config.annotation.WebMvcConfigurer{

	 @Override
	    public void addResourceHandlers(ResourceHandlerRegistry registry) {
	        registry.addResourceHandler("/asset/**")
	                .addResourceLocations("file:///C:/asset/");
	    }
	 
}
~~~

● Dto Package<br><br>

1. BoardDTO.java
~~~java
package com.green.dto;

import java.time.LocalDateTime;

import org.springframework.web.multipart.MultipartFile;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@Data
public class BoardDTO {
	
		private long boardNo;
		private String boardTitle;
		private String boardContent;
		private String boardFile;
		private MultipartFile file;
		private int viewCount;
		private int BoardWriteYear;
		private LocalDateTime regDate;
		private LocalDateTime modDate;
		private Long sectionNo;
		
}
~~~

<br>

2. PageDTO.java
~~~java
package com.green.dto;

import lombok.Data;

@Data
public class PageDTO {
	
	private int startPage;
	private int endPage;
	
	private boolean prev;
	private boolean next;
	
	private long total;
	private PagingInfo pagingInfo;
	
	public PageDTO(PagingInfo pagingInfo, long total) {
		
		this.pagingInfo = pagingInfo;
		this.total = total;
		
		this.endPage = ((pagingInfo.getPageNum()+9)/10)*10;
		this.startPage = this.endPage-9;
		
		int realEndPage = (int)(Math.ceil(this.total/(pagingInfo.getAmount()*1.0)));
		
		if (realEndPage < this.endPage) {
			this.endPage = realEndPage;
		}
		
		this.prev = startPage != 1;
		this.next = this.endPage < realEndPage;
		
	}
	
}
~~~

<br>

3. PagingInfo.java
~~~java
package com.green.dto;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class PagingInfo {
	
	private int pageNum;
	private int amount;
	
	public PagingInfo() {
		this(1, 9);
	}
	
}
~~~

<br>

● Entity Package<br><br>

1. Board.java
~~~java
package com.green.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@Entity
@Table(name="board")
@NoArgsConstructor
@AllArgsConstructor
@Getter
@Setter
@Builder
@ToString
public class Board extends BaseEntity {
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Long boardNo;
	
	@Column
	private int boardWriteYear;
	
	@Column(length = 100, nullable = false)
	private String boardTitle;
	
	@Column(length = 1000, nullable = false)
	private String boardContent;
	
	@Setter
	@Column
	private String boardFile;
	
	@Setter
	@Column(nullable = false)
	private int viewCount;
	
	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "section_no", nullable = false)
	private Section section;
	
}
~~~

<br>

2. Section.java
~~~java
package com.green.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.SequenceGenerator;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Entity
@Table(name="section")
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Getter
@ToString
public class Section {
	
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "section_seq")
	@SequenceGenerator(name = "section_seq", sequenceName = "SECTION_SEQ", allocationSize = 1)
	private Long sectionNo;
	
	@Column(length = 50, nullable = false)
	private String sectionName;

	public Section(String sectionName) {
		this.sectionName = sectionName;
	}
	
}
~~~

<br>

● Repository Package<br><br>

1. BoardRepository.java
~~~java
package com.green.repository;

import java.util.Optional;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

import com.green.entity.Board;

public interface BoardRepository extends JpaRepository<Board, Long> {
	
	@Query("select b from Board b left join b.section s where s.sectionNo =:sectionNo")
	Page<Board> getBoardListPageAll(@Param("sectionNo") Long sectionNo, Pageable pageable);
	
	@Query("select b from Board b left join b.section s where s.sectionNo =:sectionNo and b.boardWriteYear = :boardWriteYear")
	Page<Board> getBoardPageAnotherDate(@Param("sectionNo") Long sectionNo, @Param("boardWriteYear") int boardWriteYear, Pageable pagealbe);
	
	Optional<Board> findByBoardNoAndSection_sectionNo(long boardNo, Long sectionNo);
	
	@Query("select distinct max(b.boardWriteYear) from Board b left join b.section s where s.sectionNo =:sectionNo")
	Optional<Integer> getBoardDateMax(@Param("sectionNo") Long sectionNo);
	
	@Query("select distinct min(b.boardWriteYear) from Board b left join b.section s where s.sectionNo =:sectionNo")
	Optional<Integer> getBoardDateMin(@Param("sectionNo") Long sectionNo);
	
	@Query("select b from Board b where lower(b.boardTitle) like lower(concat('%', :searchResult, '%')) or " +
			   "lower(replace(b.boardTitle, ' ', '')) like lower(replace(concat('%', :searchResult, '%'), ' ', '')) " +
			   "or lower(b.boardContent) like lower(concat('%', :searchResult, '%')) or " +
			   "lower(replace(b.boardContent, ' ', '')) like lower(replace(concat('%', :searchResult, '%'), ' ', ''))")
	Page<Board> getSearchBoardResult(@Param("searchResult") String searchResult, Pageable pageable);
	
	
	@Query("select count(b) from Board b left join b.section s where sectionNo =:sectionNo")
	Optional<Integer> getBoardCount(@Param("sectionNo") Long sectionNo);
	
}
~~~

<br>

● Controller Package<br><br>

1. MainController.java
~~~java
package com.green.controller;

import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;

import org.springframework.http.ResponseEntity;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.web.csrf.CsrfToken;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.green.dto.MemberInfoDTO;
import com.green.dto.PageDTO;
import com.green.dto.PagingInfo;
import com.green.dto.ResponseMessage;
import com.green.security.dto.AuthMemberDetails;
import com.green.service.MainService;

import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;

@RestController
@RequestMapping(value={"/", "/main"})
@RequiredArgsConstructor
public class MainController {

	private final MainService service;
	
	@GetMapping("/api/csrf-token")
	public ResponseEntity<Map<String, String>> getCsrfToken(HttpServletRequest request) {
		
	    CsrfToken csrf = (CsrfToken) request.getAttribute(CsrfToken.class.getName());
	    Map<String, String> tokens = new HashMap<>();
	    tokens.put("token", csrf.getToken());
	    return ResponseEntity.ok(tokens);
	    
	}
	
	@GetMapping("/userAuthentication")
	public ResponseEntity<ResponseMessage> getUserAuth(@AuthenticationPrincipal AuthMemberDetails authMember) {
		
		if(authMember != null) {
			MemberInfoDTO memberInfo = MemberInfoDTO.builder()
					.id(authMember.getId())
					.nickName(authMember.getNickName())
					.email(authMember.getEmail())
					.roleSet(authMember.getAuthorities().stream().map(auth->auth.getAuthority()).collect(Collectors.toSet()))
					.build();
			
			if(authMember.getNickName() == null) {
				return ResponseEntity.ok().body(new ResponseMessage("추가정보 입력이 필요합니다.", null, false));
			}
			return ResponseEntity.ok().body(new ResponseMessage(memberInfo, true));
		} return ResponseEntity.ok().body(null);
		
	}
	
	@GetMapping("/search")
	public ResponseEntity<Map<String, Object>> search(@RequestParam("searchQuery") String searchQuery, PagingInfo pagingInfo) {
		
		long total = service.getSearchBoardCount(searchQuery);
		
		Map<String, Object> response = new HashMap<>();
		
		response.put("board", service.boardSearch(pagingInfo, searchQuery));
		response.put("page", new PageDTO(pagingInfo, total));
		
		return ResponseEntity.ok(response);
		
	}
	
}
~~~

<br>

2. ArchiveController.java
~~~java
package com.green.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.green.dto.BoardDTO;
import com.green.dto.CheckMessage;
import com.green.dto.PageDTO;
import com.green.dto.PagingInfo;
import com.green.service.ArchiveService;


@Controller
@RequestMapping("/archive")
public class ArchiveController {
	
	@Autowired
	ArchiveService service;
	
	@GetMapping("/mainArchive")
	public ResponseEntity<Map<String, Object>> goToMainArchiveSection() {
		
		Map<String, Object> response = new HashMap<>();
		response.put("archiveList", service.getMainArchiveList());
		return ResponseEntity.ok(response);
		
	}

	@GetMapping("/archiveAllList")
	public ResponseEntity<Map<String, Object>> goToArchive(PagingInfo pagingInfo) {
		
		Map<String, Object> response = new HashMap<>();
		long total = service.getArchiveCount(pagingInfo);
		
		response.put("list", service.getArchiveList(pagingInfo));
		response.put("maxDate", service.getMaxDate());
		response.put("minDate", service.getMinDate());
		response.put("page", new PageDTO(pagingInfo, total));
		
		return ResponseEntity.ok(response);
		
	}
	
	@GetMapping("/getArchiveListByYear")
	public ResponseEntity<Map<String, Object>> goToArchiveOne(PagingInfo pagingInfo, @RequestParam("thisYear") int thisYear) {
		
		Map<String, Object> response = new HashMap<>();

		long total = service.getArchiveCountByAnotherDate(thisYear);
		
		response.put("list", service.getArchiveListByAnotherDate(pagingInfo, thisYear));
		response.put("maxDate", service.getMaxDate());
		response.put("minDate", service.getMinDate());
		response.put("page", new PageDTO(pagingInfo, total));
		
		return ResponseEntity.ok(response);
		
	}
	
	@GetMapping("/detail/{boardNo}")
	public ResponseEntity<BoardDTO> archiveView(@PathVariable("boardNo") long boardNo, BoardDTO boardDTO) {
		
		BoardDTO response = new BoardDTO();
		
		service.archiveDetailCount(boardNo);
		response = service.getArchiveDetail(boardNo, boardDTO);
		
		return ResponseEntity.ok(response);
		
	}
	
	@PostMapping("/write")
	public ResponseEntity<CheckMessage> writeArchive(BoardDTO boardDTO) {
		
		boolean writeCheck = service.archiveWrite(boardDTO);
		
		return writeCheck
		? ResponseEntity.ok(new CheckMessage("작성 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("작성이 되지 않았습니다.", false));
		
	}
	
	@PutMapping("/modify")
	public ResponseEntity<CheckMessage> archiveModify(BoardDTO boardDTO) {
		
		boolean modifyCheck = service.archiveModify(boardDTO);
		
		return modifyCheck
		? ResponseEntity.ok(new CheckMessage("수정 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("수정되지 않았습니다.", false));
		
	}
	
	@DeleteMapping("/remove")
	public ResponseEntity<CheckMessage> archiveRemove(long boardNo) {
		
		boolean removeCheck = service.archiveRemove(boardNo);
		
		return removeCheck
		? ResponseEntity.ok(new CheckMessage("삭제 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("삭제가 되지 않았습니다.", false));
		
	}

}
~~~

<br>

3. VideoController.java
~~~java
package com.green.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import com.green.dto.BoardDTO;
import com.green.dto.CheckMessage;
import com.green.dto.PageDTO;
import com.green.dto.PagingInfo;
import com.green.service.VideoService;

@CrossOrigin
@Controller
@RequestMapping("/video")
public class VideoController {

	@Autowired
	VideoService service;
	
	@GetMapping("/mainVideo")
	public ResponseEntity<Map<String, Object>> goToMainVideoSection() {
		
		Map<String, Object> response = new HashMap<>();
		
		response.put("videoList", service.getMainVideoList());
		
		return ResponseEntity.ok(response);
		
	}
	
	@GetMapping("/videoAllList")
	public ResponseEntity<Map<String, Object>> goTovideo(PagingInfo pagingInfo) {
		
		Map<String, Object> response = new HashMap<>();
		
		long total = service.getVideoCount();
		
		response.put("list", service.getVideoList(pagingInfo));
		response.put("page", new PageDTO(pagingInfo, total));
		
		return ResponseEntity.ok(response);
		
	}
	
	@GetMapping("/detail/{boardNo}")
	public ResponseEntity<BoardDTO> videoView(@PathVariable("boardNo") Long boardNo, BoardDTO boardDTO) {
		
		BoardDTO response = new BoardDTO();
		
		service.videoDetailCount(boardNo);
		
		response = service.getVideoDetail(boardNo, boardDTO);
		
		return ResponseEntity.ok(response);
		
	}
	
	@PostMapping("/write")
	public ResponseEntity<CheckMessage> writeVideo(BoardDTO boardDTO) {
		
		boolean writeCheck = service.videoWrite(boardDTO);
		
		return writeCheck
		? ResponseEntity.ok(new CheckMessage("작성 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("작성이 되지 않았습니다.", false));
		
	}
	
	@PutMapping("/modify")
	public ResponseEntity<CheckMessage> videoModify(BoardDTO boardDTO) {
		
		boolean modifyCheck = service.videoModify(boardDTO);
		
		return modifyCheck
		? ResponseEntity.ok(new CheckMessage("수정 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("수정이 되지 않았습니다.", false));
		
	}
	
	@DeleteMapping("/remove")
	public ResponseEntity<CheckMessage> videoRemove(long boardNo) {
		
		boolean removeCheck = service.videoRemove(boardNo);
		
		return removeCheck
		? ResponseEntity.ok(new CheckMessage("삭제 됐습니다.", true))
		: ResponseEntity.ok(new CheckMessage("삭제가 되지 않았습니다.", false));
		
	}
	
}
~~~

<br>

● Service Package<br><br>

1. MainService.java
~~~java
package com.green.service;

import java.util.List;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;
import com.green.dto.SiteInfoDTO;

public interface MainService {
	
	public List<BoardDTO> boardSearch(PagingInfo pagingInfo, String searchQuery);
	public int getSearchBoardCount(String searchQuery);
	
	public SiteInfoDTO getSiteInfo();
	
}
~~~

<br>

MainServiceImpe.java
~~~java
package com.green.service;

import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;
import com.green.dto.SiteInfoDTO;
import com.green.entity.Board;
import com.green.repository.BoardRepository;
import com.green.repository.MemberRepository;
import com.green.repository.SectionRepository;

@Service
public class MainServiceImpe implements MainService {

	@Autowired
	BoardRepository br;
	
	@Autowired
	SectionRepository sr;
	
	@Autowired
	MemberRepository mr;
	
	@Override
	public List<BoardDTO> boardSearch(PagingInfo pagingInfo, String searchQuery) {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(pagingInfo.getPageNum()-1, 9, sort);
		
		Page<Board> result = br.getSearchBoardResult(searchQuery, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for (Board board : result) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}
		
		return list;
		
	}
	
	@Override
	public int getSearchBoardCount(String searchQuery) {
		
		Pageable pageable = PageRequest.of(0, 9);
		
		Page<Board> result = br.getSearchBoardResult(searchQuery, pageable);
		
		return (int)result.getTotalElements();
		
	}

	@Override
	public SiteInfoDTO getSiteInfo() {
		
		int totalContents = 0;
		int totalArchiveContents = 0;
		int totalVideoContents = 0;
		int totalMembers = 0;
		int totalMembersNotFromSocial = 0;
		int totalMembersFromSocial = 0;
		
		if (br.getBoardCount(2L).isPresent()) {
			totalArchiveContents = (Integer)br.getBoardCount(2L).get();
		}
		if (br.getBoardCount(3L).isPresent()) {
			totalVideoContents = (Integer)br.getBoardCount(3L).get();
		}
		
		totalContents = totalArchiveContents + totalVideoContents;
		
		if (mr.getMemberCountBySocial(false).isPresent()) {
			totalMembersNotFromSocial = (Integer)mr.getMemberCountBySocial(false).get();
		}
		if (mr.getMemberCountBySocial(true).isPresent()) {
			totalMembersFromSocial = (Integer)mr.getMemberCountBySocial(true).get();
		}
		
		totalMembers = totalMembersNotFromSocial + totalMembersFromSocial;
		
		SiteInfoDTO siteInfo = SiteInfoDTO.builder()
								.totalContents(totalContents)
								.totalArchiveContents(totalArchiveContents)
								.totalVideoContents(totalVideoContents)
								.totalMembers(totalMembers)
								.totalMembersFromSocial(totalMembersFromSocial)
								.build();
		
		return siteInfo;
		
	}
	
}
~~~

<br>

2. ArchiveService.java
~~~java
package com.green.service;

import java.util.List;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;

public interface ArchiveService {
	
	public List<BoardDTO> getMainArchiveList();
	
	public int getMaxDate();
	public int getMinDate();
	
	public List<BoardDTO> getArchiveList(PagingInfo pagingInfo);
	public List<BoardDTO> getArchiveListByAnotherDate(PagingInfo pagingInfo, int year);
	
	public int getArchiveCount(PagingInfo pagingInfo);
	public int getArchiveCountByAnotherDate(int year);
	
	public BoardDTO getArchiveDetail(long boardNo, BoardDTO boardDTO);
	public void archiveDetailCount(long boardNo);
	
	public boolean archiveWrite(BoardDTO boardDTO);
	public boolean archiveModify(BoardDTO boardDTO);
	public boolean archiveRemove(long boardNo);
	
}
~~~

<br>

ArchiveServiceImpe.java
~~~java
package com.green.service;

import java.io.File;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;
import com.green.entity.Board;
import com.green.repository.BoardRepository;
import com.green.repository.SectionRepository;

@Service
public class ArchiveServiceImpe implements ArchiveService{

	@Autowired
	BoardRepository br;
	
	@Autowired
	SectionRepository sr;
	
	@Override
	public int getMaxDate() {
		
		Optional<Integer> result = br.getBoardDateMax(2L);
		
		if(result.isPresent()) {
			Integer board = result.get();
			
			return board;
		}
		
		return 0;
		
	}
	
	@Override
	public int getMinDate() {
		
		Optional<Integer> result = br.getBoardDateMin(2L);
		
		if(result.isPresent()) {
			Integer board = result.get();
			
			return board;
		}
		
		return 0;
		
	}
	
	@Override
	public List<BoardDTO> getMainArchiveList() {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(0, 3, sort);
		
		Page<Board> result = br.getBoardListPageAll(2L, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for (Board board : result.getContent()) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}

		return list;
		
	}
	
	@Override
	public List<BoardDTO> getArchiveList(PagingInfo pagingInfo) {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(pagingInfo.getPageNum()-1, pagingInfo.getAmount(), sort);
		
		Page<Board> result = br.getBoardListPageAll(2L, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for(Board board : result.getContent()) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}
		
		return list;
		
	}

	@Override
	public List<BoardDTO> getArchiveListByAnotherDate(PagingInfo pagingInfo, int thisYear) {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(pagingInfo.getPageNum()-1, 9, sort);
		
		Page<Board> result = br.getBoardPageAnotherDate(2L, thisYear, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for (Board board : result.getContent()) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}
		
		return list;
		
	}
	
	@Override
	public int getArchiveCount(PagingInfo pagingInfo) {
		
		Pageable pageable = PageRequest.of(0, pagingInfo.getAmount());
		
		Page<Board> result = br.getBoardListPageAll(2L, pageable);
		
		return (int)result.getTotalElements();
		
	}
	
	@Override
	public int getArchiveCountByAnotherDate(int year) {
		
		Pageable pageable = PageRequest.of(0, 9);
		
		Page<Board> result = br.getBoardPageAnotherDate(2L, year, pageable);
		
		return (int)result.getTotalElements();
		
	}
	
	@Override
	public BoardDTO getArchiveDetail(long boardNo, BoardDTO boardDTO) {
		
		Optional<Board> result = br.findByBoardNoAndSection_sectionNo(boardNo, 2L);
		
		if (result.isPresent()) {
			Board board = result.get();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			return boardDTO;
		}
		
		return null;
		
	}
	
	@Override
	public void archiveDetailCount(long boardNo) {
		
	    Optional<Board> result = br.findByBoardNoAndSection_sectionNo(boardNo, 2L);

	    if (result.isPresent()) {
	        Board board = result.get();
	        board.setViewCount(board.getViewCount() + 1);
	        br.save(board);
	    }
	    
	}
	
	@Override
	public boolean archiveWrite(BoardDTO boardDTO) {
		
		LocalDateTime now = LocalDateTime.now();
		
		int LocalDateValue = now.getYear();
	
		String uploadDir = "C:/asset";
		
		try {
		
			Board board = Board.builder().
					boardTitle(boardDTO.getBoardTitle()).
					boardContent(boardDTO.getBoardContent()).
					boardWriteYear(LocalDateValue).
					section(sr.findById(boardDTO.getSectionNo()).orElse(null)).
					build();
			
			if (boardDTO.getFile() == null || boardDTO.getFile().isEmpty()) {
				
				br.save(board);
				
			} else {
				
				Board saveBoard = br.save(board);
				
				saveBoard.setBoardFile(saveBoard.getBoardNo()+"_"+boardDTO.getFile().getOriginalFilename());
				
				Path filePath = Paths.get(uploadDir, saveBoard.getBoardNo()+"_"+boardDTO.getFile().getOriginalFilename());
				Files.write(filePath, boardDTO.getFile().getBytes());
				
				br.save(saveBoard);
				
			}
			
			return true;
			
		}catch(IOException e) {
			return false;
		}
		
	}
	
	@Override
	public boolean archiveModify(BoardDTO boardDTO) {
		
		String uploadDir = "C:/asset";

		try {
			
			if (boardDTO.getBoardFile().equals("null")) {
				boardDTO.setBoardFile(null);
			}
			
			Board board = Board.builder().
					boardNo(boardDTO.getBoardNo()).
					boardTitle(boardDTO.getBoardTitle()).
					boardContent(boardDTO.getBoardContent()).
					boardWriteYear(boardDTO.getBoardWriteYear()).
					viewCount(boardDTO.getViewCount()).
					boardFile(boardDTO.getBoardFile()).
					section(sr.findById(boardDTO.getSectionNo()).orElse(null)).
					build();

			String imagePath = "C:/asset/"+boardDTO.getBoardFile();
			
	        File imageFile = new File(imagePath);
	        
	        if (boardDTO.getFile() == null || boardDTO.getFile().isEmpty()) {
	        		
	        	br.save(board);
	        		
	        } else {
	        		
	        	if (imageFile.exists()) {
	        		imageFile.delete();
	        	}
	        		
	        	Board saveBoard = br.save(board);
	        		
	        	saveBoard.setBoardFile(saveBoard.getBoardNo()+"_"+boardDTO.getFile().getOriginalFilename());
	        		
	        	Path filePath = Paths.get(uploadDir, boardDTO.getBoardNo()+"_"+boardDTO.getFile().getOriginalFilename());
	        	Files.write(filePath, boardDTO.getFile().getBytes());
					
	        	br.save(saveBoard);
					
	        }
	        	
	        return true;
		
		}catch(Exception e) {
			return false;
		}
	
	}
	
	@Override
	public boolean archiveRemove(long boardNo) {
		
		Optional<Board> result = br.findById(boardNo);

		if (result.isPresent()) {
			
			Board board = result.get();
				
			String uploadDir = "C:/asset/";
				
			String imagePath = uploadDir + board.getBoardFile();
		        
			File imageFile = new File(imagePath);
			
			if (br.existsById(boardNo)) {
		        	
				if (imageFile.exists()) {
		        	imageFile.delete();
		        }
		        	
		        br.deleteById(boardNo);
		        	
		        return true;
		        	
			}

		}
			
		return false;
		
	}

}
~~~

<br>

3. VideoService.java
~~~java
package com.green.service;

import java.util.List;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;

public interface VideoService {
	
	public List<BoardDTO> getMainVideoList();
	
	public List<BoardDTO> getVideoList(PagingInfo pagingInfo);
	public int getVideoCount();
	
	public BoardDTO getVideoDetail(long boardNo, BoardDTO boardDTO);
	public void videoDetailCount(long boardNo);

	public boolean videoWrite(BoardDTO boardDTO);
	public boolean videoModify(BoardDTO boardDTO);
	public boolean videoRemove(long boardNo);
	
}
~~~

<br>

VideoServiceImpe.java
~~~java
package com.green.service;

import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;

import com.green.dto.BoardDTO;
import com.green.dto.PagingInfo;
import com.green.entity.Board;
import com.green.repository.BoardRepository;
import com.green.repository.SectionRepository;

@Service
public class VideoServiceImpe implements VideoService {
	
	@Autowired
	BoardRepository br;
	
	@Autowired
	SectionRepository sr;
	
	@Override
	public List<BoardDTO> getMainVideoList() {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(0, 3, sort);
		
		Page<Board> result = br.getBoardListPageAll(3L, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for (Board board : result.getContent()) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}
		
		return list;
		
	}
	
	@Override
	public List<BoardDTO> getVideoList(PagingInfo pagingInfo) {
		
		Sort sort = Sort.by("regDate").descending();
		
		Pageable pageable = PageRequest.of(pagingInfo.getPageNum()-1, pagingInfo.getAmount()-1, sort);
		
		pagingInfo.setAmount(pagingInfo.getAmount()-1);

		Page<Board> result = br.getBoardListPageAll(3L, pageable);
		
		List<BoardDTO> list = new ArrayList<>();
		
		for (Board board : result.getContent()) {
			
			BoardDTO boardDTO = new BoardDTO();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardWriteYear(board.getBoardWriteYear());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			list.add(boardDTO);
			
		}
		
		return list;
		
	}
	
	@Override
	public int getVideoCount() {
		
		Pageable pageable = PageRequest.of(0, 8);
		
		Page<Board> result = br.getBoardListPageAll(3L, pageable);
		
		return (int)result.getTotalElements();
		
	}
	
	@Override
	public BoardDTO getVideoDetail(long boardNo, BoardDTO boardDTO) {
		
		Optional<Board> result = br.findByBoardNoAndSection_sectionNo(boardNo, 3L);
		
		if (result.isPresent()) {
			
			Board board = result.get();
			
			boardDTO.setBoardNo(board.getBoardNo());
			boardDTO.setBoardTitle(board.getBoardTitle());
			boardDTO.setBoardContent(board.getBoardContent());
			boardDTO.setRegDate(board.getRegDate());
			boardDTO.setModDate(board.getModDate());
			boardDTO.setBoardFile(board.getBoardFile());
			boardDTO.setViewCount(board.getViewCount());
			boardDTO.setSectionNo(board.getSection().getSectionNo());
			
			return boardDTO;

		}
		
		return null;
		
	}
	
	@Override
	public void videoDetailCount(long boardNo) {
		
	    Optional<Board> result = br.findByBoardNoAndSection_sectionNo(boardNo, 3L);

	    if (result.isPresent()) {
	        Board board = result.get();
	        board.setViewCount(board.getViewCount() + 1);
	        br.save(board);
	    }
	    
	}
	
	@Override
	public boolean videoWrite(BoardDTO boardDTO) {
		
		LocalDateTime now = LocalDateTime.now();
		
		int LocalDateValue = now.getYear();
		
		try {
			
			Board board = Board.builder().
					boardTitle(boardDTO.getBoardTitle()).
					boardContent(boardDTO.getBoardContent()).
					boardFile(boardDTO.getBoardFile()).
					boardWriteYear(LocalDateValue).
					section(sr.findById(boardDTO.getSectionNo()).orElse(null)).
					build();
			
			board = br.save(board);
			
			return true;
			
		}catch(Exception e) {
			return false;
		}
		
	}
	
	@Override
	public boolean videoModify(BoardDTO boardDTO) {
		
		try {
			
			Board board = Board.builder().
					boardNo(boardDTO.getBoardNo()).
					boardTitle(boardDTO.getBoardTitle()).
					boardContent(boardDTO.getBoardContent()).
					boardFile(boardDTO.getBoardFile()).
					boardWriteYear(boardDTO.getBoardWriteYear()).
					viewCount(boardDTO.getViewCount()).
					section(sr.findById(boardDTO.getSectionNo()).orElse(null)).
					build();
			br.save(board);
			
			return true;
			
		}catch(Exception e) {
			return false;
		}
		
	}
	
	@Override
	public boolean videoRemove(long boardNo) {
		
		if (br.existsById(boardNo)){
			br.deleteById(boardNo);
			return true;
		} else {
			return false;
		}
	
	}
	
}
~~~
